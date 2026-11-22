# The Virtual Private Phone (VPP)
### Decoupled Physical Architecture for Anti-Forensic Mobile Security & Resilient Digital Identity
#### An Architectural & Research Proposal

[![Build and Release Thesis PDF](https://github.com/Z23599848/virtual-private-phone-thesis/actions/workflows/latex.yml/badge.svg)](https://github.com/Z23599848/virtual-private-phone-thesis/actions/workflows/latex.yml)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

> **Researcher:** [Ali Ganji](https://xiii.me)  
> **Affiliation:** LabXIII Research (`aliganji@labxiii.org`)  
> **Repository:** [https://github.com/Z23599848/virtual-private-phone-thesis](https://github.com/Z23599848/virtual-private-phone-thesis)  
> **Document Type:** Research & Architectural Proposal (Awaiting Reproducible Prototype Benchmarking)

---

## 📖 Vision & The Story

> *Are you a criminal whose phone keeps getting cast as Exhibit A? A hacker whose camera roll could fill six episodes of a Netflix documentary? I have an idea for the phone part. Your lawyer can handle the screenplay.* 🙂

Imagine you're caught. The cuffs click, and suddenly your phone is in police hands. Frankly, regardless of guilt or innocence, **it is fundamentally unfair to personal privacy that the police can just dig and dive into your entire life simply because they took a piece of hardware from your pocket.**

The problem started with an ordinary frustration: lose your phone, borrow someone else's to log in, and where does the 2FA code go? Straight to the phone you just lost. We ask one pocket-sized device to hold our private lives, prove who we are, and then betray us the moment it leaves our hands.

The solution is the **Virtual Private Phone (VPP)**:
> *"One phone stays home. The other goes in your pocket."*

- **The Backend ("Home Phone"):** Stays plugged in safely at home. It holds your primary SIM card, runs your apps, and stores your encrypted messages, photos, and files.
- **The Frontend ("Pocket Handset"):** An expendable, stateless piece of hardware in your pocket. It has a generic data connection, giving you a screen, camera, microphone, and touchscreen—streaming live from home with zero local user storage.

If the police seize your pocket phone—**whether you give them the password or not**—they get literally nothing. If they put it in a Faraday bag or you dial an emergency revocation hotline, the session cuts instantly and RAM wipes in under 50 milliseconds. The police are left holding a dead piece of glass, while your entire digital life remains intact at home.

---

## 🏛 Architectural Blueprint

```
                      +------------------------------------------+
                      |         UNTRUSTED EDGE (Pocket)          |
                      |          Frontend Handset (F)            |
                      |  - RAM-only ephemeral OS (dm-verity)     |
                      |  - Touch / Screen / Camera / Mic HAL     |
                      |  - Zero persistent user databases        |
                      +--------------------+---------------------+
                                           |
                                  WireGuard / QUIC Tunnel
                                 (AV1 Video + Touch Ingest)
                                           |
                      +--------------------+---------------------+
                      |         TRUSTED REDOUBT (Home)           |
                      |           Backend Core (B)               |
                      |  - Primary eSIM/pSIM Baseband            |
                      |  - Encrypted NVMe Storage                |
                      |  - Headless Android Container            |
                      |  - Hardware Root of Trust (HSM)          |
                      +--------------------+---------------------+
                                           ^
                                           |
                             Authenticated OOB Phone Call
                                 (PIN + Challenge)
                                           |
                      +--------------------+---------------------+
                      |        OOB Emergency Hotline             |
                      |      (Twilio / Redundant IVR)            |
                      +------------------------------------------+
```

---

## 📂 Repository Structure

The research paper is written in clean, modular LaTeX:

```
├── .github/
│   └── workflows/
│       └── latex.yml           # Automated CI/CD PDF compiler & GitHub release
├── sections/
│   ├── 01_introduction.tex                  # Motivation, operational scenarios, contributions
│   ├── 02_background_and_related_work.tex   # Mobile forensics, Cellebrite, GrayKey, BFU vs AFU, MDM
│   ├── 03_threat_model.tex                  # Formal threat actors (A1-A4), trust boundaries, properties
│   ├── 04_vpp_architecture.tex              # Bifurcated design, RAM-only OS, V-HAL sensor loops
│   ├── 05_cryptographic_protocols.tex       # Noise IKpsk2, OOB revocation engine, memory zeroization
│   ├── 06_telephony_and_peripherals.tex     # Physical SIM bridging, zero-remanence camera, GPS cloaking
│   ├── 07_anti_forensic_evaluation.tex      # Two-Handset benchmark, extraction tests, latency metrics
│   ├── 08_limitations_and_future_work.tex   # Network bounds, CBR traffic shaping, legal/evidentiary
│   └── 09_conclusion.tex                    # Summary & future roadmap
├── main.tex                    # Master LaTeX configuration file
├── references.bib              # Comprehensive academic bibliography
└── README.md                   # Project overview and researcher documentation
```

---

## 🚀 How to Compile the Thesis

### Option 1: Automated Cloud Build (GitHub Actions)
Every `git push` automatically compiles the modular LaTeX sources using GitHub Actions (`xu-cheng/latex-action`). Download the compiled `main.pdf` directly from the repository's **Actions** tab under **Artifacts**.

### Option 2: Local Compilation (TeX Live / MacTeX / MiKTeX)
```bash
git clone https://github.com/Z23599848/virtual-private-phone-thesis.git
cd virtual-private-phone-thesis

# Compile with pdflatex and bibtex
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

### Option 3: Overleaf / Cloud LaTeX
1. Download or clone this repository as a `.zip`.
2. Go to [Overleaf](https://www.overleaf.com), click **New Project** $\to$ **Upload Project**.
3. Set `main.tex` as the root document and compile with **pdfLaTeX**.

---

## 🔬 Proposed Verification Framework & Validation Criteria

This proposal specifies two concrete experimental protocols that must be reproducibly satisfied prior to any production deployment:

1. **Protocol I: The Two-Handset Table Benchmark:** When Handset $F_A$ is placed on a table unlocked in an AFU state and an out-of-band revocation call is initiated, the prototype must achieve:
   - Backend cryptographic peer severance within $\le 50$\,ms.
   - Frontend hardware framebuffer and memory zeroization within $\le 50$\,ms.
   - Zero recoverable chat databases, private keys, or media files under full physical chip extraction (Cellebrite UFED / GrayKey profiles).
   - Operational session recovery on a cold-spare replacement (Handset $F_B$) within $\le 25$\,seconds.
2. **Protocol II: The Outdoor Mobility & Sensor Benchmark:** The prototype must undergo field validation walking off domestic Wi-Fi onto cellular 5G:
   - Seamless cellular handover with re-routing latency $\le 15$\,ms.
   - Transparent incoming phone calls terminating at the home pSIM and ringing the pocket device with round-trip audio latency $\le 35$\,ms.
   - Zero-remanence camera capture: photographing physical documents saves the original to the domestic core while leaving zero metadata or flash remnants on the pocket handset.
3. **Motion-to-Photon Latency Budget:** Maintaining $\le 42.5$\,ms end-to-end touch latency under commercial 5G networks at 60 fps AV1 video streaming to guarantee perceptual parity with local smartphones.

---

## 👤 Researcher & Citation

**Ali Ganji**  
LabXIII Research  
Website: [https://xiii.me](https://xiii.me)  
Email: `aliganji@labxiii.org`  
GitHub: [@Z23599848](https://github.com/Z23599848)

```bibtex
@article{ganji2026vpp,
  author    = {Ali Ganji},
  title     = {The Virtual Private Phone (VPP): Decoupled Physical Architecture for Anti-Forensic Mobile Security and Resilient Digital Identity},
  journal   = {LabXIII Research Publications},
  year      = {2026},
  url       = {https://github.com/Z23599848/virtual-private-phone-thesis}
}
```
