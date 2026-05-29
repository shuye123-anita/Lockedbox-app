Skip to content
shuye123-anita
Lockedbox-app
Repository navigation
Code
Issues
Pull requests
Agents
Actions
Projects
Wiki
Security and quality
Insights
Settings
Files
Go to file
t
T
README.md
Lockedbox-app
/
Name your file...
in
main

Edit

Preview

# 🔐 LockedBox

> **Biometrically Protected Document Vault**  
> Final Year Project — [SHUYE ANITA BERINYU] | [ICT University] | [2026]

[![Flutter](https://img.shields.io/badge/Flutter-3.x-02569B?logo=flutter)](https://flutter.dev)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Android%20%7C%20iOS-lightgrey)](https://flutter.dev)
[![Security](https://img.shields.io/badge/Encryption-AES--256--GCM-green)]()

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [System Architecture](#-system-architecture)
- [Security Design](#-security-design)
- [Tech Stack](#-tech-stack)
- [Installation & Setup](#-installation--setup)
- [Usage Guide](#-usage-guide)
- [Implementation Details](#-implementation-details)
  - [Liveness Detection Gate](#1-liveness-detection-gate)
  - [AES-256 Encryption Engine](#2-aes-256-encryption-engine)
  - [EXIF Metadata Scrubber](#3-exif-metadata-scrubber)
  - [Secure Document Sharing](#4-secure-document-sharing)
- [Project Structure](#-project-structure)
- [Screenshots](#-screenshots)
- [Limitations & Future Work](#-limitations--future-work)
- [Author](#-author)
- [License](#-license)

---

## 📌 Overview

**LockedBox** is a mobile application designed to give users a fully private and encrypted space to store sensitive documents. Unlike cloud storage solutions that process and index your files on their servers, LockedBox ensures that your documents are encrypted **on-device**, protected by your **face** (liveness-verified), and that no readable copy of your files ever leaves your control.

The application was developed as a final year project exploring the intersection of **biometric authentication**, **cryptography**, and **privacy-preserving file management** on mobile platforms.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🎭 **Liveness Detection Gate** | Requires the user to smile or blink before access is granted — prevents photo/video spoofing |
| 🔒 **AES-256-GCM Encryption** | All vault contents are encrypted with a biometric-derived key. Files are unreadable without authentication |
| 🧹 **EXIF Metadata Scrubbing** | All hidden metadata (GPS, timestamps, device info) is stripped from files on ingestion |
| 📤 **Secure Document Sharing** | End-to-end encrypted sharing between LockedBox users via asymmetric key exchange |
| 📂 **Multi-format Support** | Supports JPEG, PNG, PDF, and DOCX file types |
| ⏳ **Auto-lock** | Vault automatically locks after a configurable inactivity period |
| 🔁 **Exponential Lockout** | After repeated authentication failures, access is locked with increasing delay |

---

## 🏗 System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     LockedBox App                        │
│                                                         │
│  ┌──────────────┐    ┌──────────────┐    ┌───────────┐  │
│  │  Biometric   │    │  Encryption  │    │   EXIF    │  │
│  │  Auth Layer  │───▶│   Engine     │    │  Scrubber │  │
│  │              │    │  AES-256-GCM │    │           │  │
│  │  Liveness    │    │  Argon2 KDF  │    │  On-ingest│  │
│  │  Detection   │    │              │    │  stripping│  │
│  └──────────────┘    └──────────────┘    └───────────┘  │
│         │                   │                  │         │
│         ▼                   ▼                  ▼         │
│  ┌────────────────────────────────────────────────────┐  │
│  │              Encrypted Local Vault                  │  │
│  │         (SQLCipher + Encrypted File Store)          │  │
│  └────────────────────────────────────────────────────┘  │
│                           │                             │
│                           ▼                             │
│  ┌────────────────────────────────────────────────────┐  │
│  │          Secure Sharing Module (Optional)           │  │
│  │     ECDH Key Exchange + Zero-Knowledge Server       │  │
│  └────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

---

## 🛡 Security Design

LockedBox is built on the principle of **defense in depth** — multiple independent security layers, each of which must be compromised independently for an attacker to gain access.

### Threat Model

| Threat | Mitigation |
|---|---|
| Photo/video face spoofing | Liveness detection (smile/blink challenge) |
| Device theft with screen unlock | AES-256 encrypted vault; key requires biometric re-derivation |
| Hidden file metadata leaking location | EXIF scrubbing on all ingested files |
| Man-in-the-middle on file sharing | End-to-end encryption; server holds only ciphertext |
| Brute force authentication | Exponential lockout; key never stored on disk |
| Reverse engineering the app | Biometric key derived at runtime; not persisted anywhere |

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| **Mobile Framework** | Flutter (Dart) |
| **Face / Liveness Detection** | Google ML Kit Face Detection |
| **Symmetric Encryption** | AES-256-GCM via `cryptography` package |
| **Key Derivation** | Argon2id (memory-hard KDF) |
| **Secure Key Storage** | Android Keystore / iOS Secure Enclave |
| **EXIF Stripping** | `image` package (JPEG/PNG), `syncfusion_flutter_pdf` |
| **Asymmetric Sharing** | ECDH Curve25519 + libsodium sealed boxes |
| **Local Database** | SQLite with SQLCipher (encrypted) |
| **Backend (sharing only)** | Firebase / Supabase — stores public keys and encrypted blobs only |

---

## 🚀 Installation & Setup

### Prerequisites

- Flutter SDK ≥ 3.0
- Android Studio / Xcode
- A physical device (camera required for liveness detection — emulators will not work)

### Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/lockedbox-app.git
cd lockedbox-app
```

### Install Dependencies

```bash
flutter pub get
```

### Configure Firebase (for Secure Sharing feature)

1. Create a Firebase project at [console.firebase.google.com](https://console.firebase.google.com)
2. Download `google-services.json` (Android) and `GoogleService-Info.plist` (iOS)
3. Place them in `android/app/` and `ios/Runner/` respectively
4. Enable **Firestore** and **Authentication** in the Firebase console

### Run the App

```bash
# Android
flutter run

# iOS (requires Mac + Xcode)
flutter run --dart-define=PLATFORM=ios
```

---

## 📱 Usage Guide

### First Launch

1. Open LockedBox — you will be prompted to complete **biometric enrollment**
2. Look at the camera and follow the on-screen prompt (smile or blink)
3. Your vault is created and locked to your biometric profile
4. Add documents using the **+** button in the vault

### Adding a Document

1. Tap **+** → choose a file from your device storage
2. The app automatically **strips all EXIF metadata** from the file
3. The file is **encrypted** and stored in the private vault
4. A confirmation banner shows: ✅ *Metadata Removed* | ✅ *Encrypted*

### Sharing a Document

1. Long-press a document → tap **Share Securely**
2. Enter the recipient's LockedBox username
3. The document is re-encrypted with the recipient's public key
4. The recipient receives a notification and must pass liveness check to decrypt

---

## 🔬 Implementation Details

### 1. Liveness Detection Gate

The liveness gate prevents spoofing attacks using printed photos or replay videos. It works through a **challenge-response** mechanism:

- On each app open, the system randomly selects a challenge: **smile** or **blink**
- Using **Google ML Kit Face Detection**, facial landmarks are tracked in real time
- **Smile detection**: the ratio of lip corner distance to face width is measured; above a threshold confirms a genuine smile
- **Blink detection**: Eye Aspect Ratio (EAR) is computed each frame; a drop below 0.2 sustained for 2+ frames confirms a blink

```
EAR = (|p2 - p6| + |p3 - p5|) / (2 × |p1 - p4|)
```
*Where p1–p6 are the six eye landmark points per eye.*

On 3 consecutive failures, the app enforces an exponential backoff lockout (30s → 5min → 30min).

---

### 2. AES-256 Encryption Engine

**Key Derivation:**

The encryption key is never stored. It is derived fresh each session from biometric data:

```
Face Landmark Feature Vector
        +
Device Salt (Android Keystore / iOS Secure Enclave)
        │
        ▼
   Argon2id KDF
        │
        ▼
  256-bit AES Key (lives in RAM only)
```

**File Encryption:**

All files are encrypted using **AES-256-GCM**, which provides both confidentiality (encryption) and integrity (authentication tag). The encrypted blobs are stored with a `.lbx` extension in the app's private directory, inaccessible to other apps.

```
Original File ──▶ AES-256-GCM Encrypt ──▶ .lbx Encrypted Blob
                        │
                   (key in RAM,
                   never written
                     to disk)
```

---

### 3. EXIF Metadata Scrubber

All files are scrubbed of metadata **before** encryption and storage. The original is never persisted.

| File Type | Metadata Removed |
|---|---|
| JPEG / PNG | GPS coordinates, timestamp, camera make/model, software info |
| PDF | Author, creation date, XMP metadata, document title |
| DOCX | Author, last modified by, revision history, creation date |

The scrubber rewrites the file from scratch rather than patching headers, ensuring no metadata remnants survive in comment fields or secondary structures.

---

### 4. Secure Document Sharing

LockedBox uses **end-to-end encryption** for sharing. The backend server acts as a **zero-knowledge relay** — it stores only public keys and ciphertext. No plaintext ever leaves the sender's device.

**Protocol:**

```
Alice (Sender)                    Server                   Bob (Recipient)
──────────────                 ──────────────           ──────────────────
                                                   [Bob has RSA/ECDH keypair]
                                                   [Public key registered]

1. Alice fetches Bob's public key ◀──────── Public key lookup ──────────────
2. Alice generates ephemeral session key
3. File re-encrypted: AES(session_key) + ECDH-sealed(session_key, Bob.pubkey)
4. Encrypted package uploaded ──────────────────────────▶ Stored (ciphertext only)

5.                                                   Bob opens app, passes liveness
6.                                                   Bob's private key (Secure Enclave)
                                                     decrypts session key
7.                                                   Session key decrypts document
8.                                                   Plaintext exists ONLY on Bob's device
```

**Security properties:**
- **Zero-knowledge server**: server never sees plaintext content
- **Forward secrecy**: ephemeral session keys; compromise of long-term keys does not expose past messages
- **Expiring shares**: packages auto-delete from server after 48 hours or first successful download
- **Share receipts**: sender notified when recipient successfully decrypts (without revealing content)

---

## 📁 Project Structure

```
lockedbox-app/
├── lib/
│   ├── auth/
│   │   ├── liveness_detector.dart      # Face landmark analysis, challenge logic
│   │   ├── key_derivation.dart         # Argon2 KDF, biometric feature extraction
│   │   └── lockout_manager.dart        # Exponential backoff logic
│   ├── crypto/
│   │   ├── aes_engine.dart             # AES-256-GCM encrypt/decrypt
│   │   └── ecdh_exchange.dart          # Curve25519 key exchange for sharing
│   ├── vault/
│   │   ├── vault_manager.dart          # CRUD operations on encrypted vault
│   │   ├── exif_scrubber.dart          # Metadata stripping for all file types
│   │   └── file_importer.dart          # File ingestion pipeline
│   ├── sharing/
│   │   ├── share_manager.dart          # Orchestrates the sharing protocol
│   │   └── key_registry.dart           # Fetches/publishes public keys
│   ├── ui/
│   │   ├── screens/
│   │   │   ├── liveness_screen.dart
│   │   │   ├── vault_screen.dart
│   │   │   ├── document_viewer.dart
│   │   │   └── share_screen.dart
│   │   └── widgets/
│   └── main.dart
├── assets/
│   └── models/                         # ML model files if bundled
├── test/
│   ├── crypto_test.dart
│   ├── exif_test.dart
│   └── liveness_test.dart
├── docs/
│   ├── architecture.png
│   ├── security_design.md
│   └── threat_model.md
├── README.md
├── LICENSE
└── pubspec.yaml
```

---

## 📸 Screenshots

> *(Add screenshots here after UI is built)*

| Liveness Gate | Vault Home | Document View | Secure Share |
|---|---|---|---|
| ![](docs/screenshots/liveness.png) | ![](docs/screenshots/vault.png) | ![](docs/screenshots/viewer.png) | ![](docs/screenshots/share.png) |

---

## ⚠️ Limitations & Future Work

### Current Limitations

- Liveness detection requires good lighting conditions
- Biometric enrollment is device-specific; vault cannot be restored to a new device without a backup key
- Sharing requires both users to have the app installed

### Planned Improvements

- [ ] Encrypted cloud backup using a user-supplied passphrase
- [ ] Multi-biometric support (fingerprint fallback)
- [ ] Self-destructing shares (burn after read)
- [ ] Audit log of all vault access events (stored encrypted)
- [ ] Desktop companion app for vault access on PC/Mac

---

## 👤 Author

**SHUYE ANITA BERINYU**  
Final Year Project — [Cyber Security] 
ICT University  
📧 [shuye.berinyu@ictuniversity.edu]  
🔗 [LinkedIn / GitHub profile]

**Supervisor:** [Dr.Fosso], Biometrically protected document vault with AES-256 encryption, liveness detection, and secure sharing

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

> *"Security is not a product, but a process."* — Bruce Schneier
No spaces found. You can create a new space to get started.
