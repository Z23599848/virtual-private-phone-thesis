# The Virtual Private Phone (VPP)
### Decoupled Physical Architecture for Anti-Forensic Mobile Security & Resilient Digital Identity

[![Build and Release Thesis PDF](https://github.com/Z23599848/virtual-private-phone-thesis/actions/workflows/latex.yml/badge.svg)](https://github.com/Z23599848/virtual-private-phone-thesis/actions/workflows/latex.yml)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

> **Researcher:** [Ali Ganji](https://xiii.me)  
> **Affiliation:** LabXIII Research (`contact@xiii.me`)  
> **Repository:** [https://github.com/Z23599848/virtual-private-phone-thesis](https://github.com/Z23599848/virtual-private-phone-thesis)  
> **Thesis / Academic Paper:** Final-Year Capstone Thesis in Mobile Forensics & Cryptographic Security

---

## 📖 Vision & Core Premise

> *"One phone stays home. The other goes in your pocket."*  
> *"I want to be able to lose possession of the handset without losing control of the phone."*

Every modern smartphone conflates two incompatible security concepts into a single physical unit:
1. **The Human-Computer Interface (HCI):** A portable glass-and-aluminum device carried into hostile terrain, subject to police confiscation, border seizure, opportunistic theft, and surveillance.
2. **The Root of Trust:** A vault containing private keys, banking apps, encrypted chat databases, primary cellular subscriber credentials (pSIM/eSIM), and 2FA recovery channels.

When an ordinary phone is seized in an **After First Unlock (AFU)** state—or placed in a Faraday bag to defeat over-the-air (OTA) remote wipes—forensic tools such as **Cellebrite UFED** and **Magnet GrayKey** extract complete plaintext databases.

The **Virtual Private Phone (VPP)** eliminates this failure mode by decoupling the mobile architecture:
- **The Backend ("Home Core"):** Kept in a secure domestic redoubt on continuous power and residential fiber. Hosts the physical SIM card, full-disk encrypted NVMe storage, application runtime, and root-of-trust HSM.
- **The Frontend ("Pocket Handset"):** A stateless, ephemeral client running an immutable, RAM-only hardened OS with hardware peripheral pass-through (camera, mic, touchscreen, display, IMU) over an authenticated WireGuard/QUIC tunnel.
- **Out-of-Band (OOB) Revocation Protocol:** If the frontend is confiscated or seized, an authenticated telephone call to a 24/7 hotline severs the session on the backend. The frontend retains zero decrypted user data, zero application databases, and no valid cryptographic keys.

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

## 🔬 Key Experimental Findings

1. **Anti-Forensic Integrity:** When Handset $F_A$ was seized in an active, unlocked AFU state, out-of-band revocation severed the session within **18 ms**. The frontend hardware framebuffer zeroized within **42 ms**. Full physical chip acquisition revealed zero chat databases, zero SMS records, zero cached photos, and no route back to the home core.
2. **Cold Replacement Speed:** Activating a cold spare frontend (Handset $F_B$) and restoring the entire operating environment took **21.1 seconds** total, without needing to reinstall applications or reconfigure 2FA tokens.
3. **Motion-to-Photon Latency:** On commercial 5G networks, end-to-end touch latency measured **40.1 ms** with 60 fps AV1 video streaming, achieving full perceptual parity with local smartphones.

---

## 👤 Researcher & Citation

**Ali Ganji**  
LabXIII Research  
Website: [https://xiii.me](https://xiii.me)  
Email: `contact@xiii.me`  
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
