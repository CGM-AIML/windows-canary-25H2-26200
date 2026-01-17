# Windows 11 Canary 25H2 (Build 26200.x)  
## Imaging Stack Regression Under Advanced GPU and WSL Workloads

---

## Repository Purpose

This repository documents a real-world runtime regression observed on **Windows 11 Insider Canary, version 25H2 (Build 26200.x)**.  

The goal of this project is not to provide a workaround or fix, but to **document, analyze, and contextualize** how experimental Canary builds behave under advanced workloads that stress GPU compute, virtualization, and desktop media pipelines simultaneously.

This repository is intended for:
- Systems and platform engineers
- GPU and performance engineers
- Advanced Windows Insider participants
- AI and ML practitioners operating close to OS boundaries
- Anyone interested in how Controlled Feature Rollout behaves in practice

---

## What the Canary Channel Is

Microsoft defines the **Canary Channel** as the earliest and least stable Windows Insider channel. According to official Windows Insider documentation:

- Canary builds represent **early platform changes**
- They are **not tied to a specific final Windows release**
- Features and behaviors may change, be removed, or never ship
- Canary builds often include **foundational OS experiments**
- Many changes are delivered via **Controlled Feature Rollout (CFR)**

Controlled Feature Rollout means that runtime code paths may be enabled gradually, selectively, or dynamically, and may differ between devices or over time even on the same build.

This delivery model explicitly allows for scenarios where:
- Static system checks succeed
- The OS appears healthy at rest
- Failures occur only under specific runtime conditions

---

## Build Context and Baseline Model

### Windows 11 Version 25H2 and Build 26200

Microsoft established **Build 26200** as the baseline for **Windows 11, version 25H2** in the Canary Channel.

Important clarifications:

- `26200` is the **baseline identifier**
- Builds such as `26200.5074`, `26200.5761`, or `26200.7462` are **serviced cumulative updates** on the same baseline
- Microsoft does not treat every `26200.xxxx` suffix as a separate headline build
- Official dashboards track the baseline, not every serviced revision

This is why Flight Hub lists **Build 26200** for 25H2 but does not enumerate every serviced suffix.

### Initial Public Release

- Windows 11 Insider Preview Build 26200 (Canary Channel)
- Initial public release: **April 3, 2024**

Later `26200.xxxx` builds represent continued Canary servicing on the same experimental baseline.

---

## Why Controlled Feature Rollout Matters Here

Microsoft documents CFR as a phased delivery mechanism used across Windows 11, including Insider builds.

Key implications of CFR:

- Static integrity checks such as SFC and DISM can report a healthy system
- Required binaries and features can be present and versioned correctly
- Applications can be installed and up to date
- Failures can still occur when a **specific runtime code path is activated**

This explains common Canary behavior patterns such as:
- “It worked previously”
- “Nothing obvious changed”
- “The system broke under a specific workload”

This project documents a failure pattern that matches CFR behavior precisely.

---

## System Configuration Summary

The observed regression occurred on a high-end AI workstation configured for advanced GPU workloads, including:

- Windows 11 Insider Canary, version 25H2 (Build 26200.x)
- Hybrid GPU configuration (Intel integrated GPU and NVIDIA discrete GPU)
- WSL2 with GPU passthrough enabled
- Long-lived CUDA compute workloads
- High VRAM and BAR1 utilization
- Frequent transitions between Linux GPU compute and Windows desktop usage

This workload combination is uncommon among most Canary users.

---

## Observed Issue Summary

After a sustained GPU compute workload executed inside WSL2, the following behavior was observed:

- Screenshot files are successfully created on disk
- Image rendering fails across:
  - Paint
  - Photos
  - Explorer image preview
- Snipping Tool crashes immediately after capture
- The issue reproduces across all user profiles
- Reboots do not resolve the issue
- System integrity checks report no corruption
- AppX re-registration, media feature rebinding, and driver updates do not resolve the issue

The failure consistently manifests during the transition from GPU compute back to Windows desktop media rendering.

---

## What Was Verified

Extensive system-level auditing confirmed:

- OS component store integrity is intact
- Application packages (Paint, Photos, Snipping Tool) are installed and current
- Media Foundation and Windows Imaging Component binaries are present and versioned
- The issue is not profile-scoped
- The issue is not due to missing features or codecs
- GPU compute remains functional
- WSL2 remains healthy and operational

This rules out classic system corruption or misconfiguration.

---

## Why This Issue Is Rare

Most Canary users do not simultaneously stress:

- WSL2 with GPU passthrough
- Long-lived CUDA compute contexts
- High VRAM and BAR1 pressure
- Immediate return to Windows desktop media capture and rendering

Canary regressions often surface at the boundaries between subsystems. This case sits at the intersection of:

- GPU compute
- Virtualization
- Desktop Window Manager (DWM)
- Media Foundation
- Windows Imaging Component (WIC)

This significantly narrows the population likely to encounter the issue.

---

## Alignment With Official Canary Documentation

The observed behavior aligns with Microsoft’s documented expectations for Canary builds:

- Canary is explicitly experimental and unstable
- CFR can activate new runtime paths dynamically
- Local repair is often not supported for CFR-triggered regressions
- Resolution typically occurs through:
  - Subsequent Canary cumulative updates
  - Re-baselining the same Canary branch

This is consistent with a **runtime regression in an experimental OS branch**, not a damaged installation.

---

## Capture Limitations

Due to the failure occurring within the Windows desktop composition and imaging stack, **all local software-based screen capture tools were non-functional**, including third-party utilities that typically bypass the Snipping Tool.

This indicates the failure exists **below the application layer**, within the desktop rendering or duplication surface itself.

Visual evidence was captured using **out-of-band methods** that do not rely on the local Windows desktop rendering pipeline, such as:
- Remote session capture
- Legacy Windows Steps Recorder
- External recording

---

## Community Cross-Validation

In addition to Microsoft documentation, this investigation cross-referenced:

- ElevenForum build tracking threads for the 26200 baseline
- Microsoft Learn Q&A discussions related to Insider flighting and CFR
- Secondary Windows commentary sites used for trend validation only

These sources reinforce that:
- Build 26200 is treated as a baseline
- CFR-driven variability is expected in Canary
- Build-to-build regressions occur regularly in early platform experimentation

Community sources are not treated as authoritative, but are useful for identifying patterns and corroborating observations.

---

## Project Status

This investigation is ongoing.

Future updates will document:
- Behavior changes across new Canary cumulative updates
- Results of controlled Canary re-baseline testing
- Additional findings related to GPU and media subsystem interaction

This repository is maintained as a **living case study**.

### Representative SDXL Outputs

These images are representative final outputs generated during the documented SDXL session.

All images are fully decoded JPGs produced with SDXL + Refiner, 38 denoising steps, CFG 6.8,
locked seeds, and long-token prompts.

#### Example 1
![1152×1536](docs/images/00064-2026-01-11-sd_xl_base_1.0.jpg)

#### Example 2
![1152×1536](docs/images/00068-2026-01-11-sd_xl_base_1.0.jpg)

#### Example 3
![1024×1024](docs/images/00083-2026-01-11-tempestByVlad_baseV01.jpg)

#### Example 4
![1024×1024](docs/images/00190-2026-01-11-tempestByVlad_baseV01.jpg)

#### Example 5
![1024×1024](docs/images/00191-2026-01-11-tempestByVlad_baseV01.jpg)

#### Example 6
![1152×1152](docs/images/00196-2026-01-11-tempestByVlad_baseV01.jpg)

#### Example 7
![848×1152](docs/images/00346-2026-01-11-TempestV0.1-Artistic.jpg)

#### Example 8
![896×1344](docs/images/00376-2026-01-11-TempestV0.1-Artistic.jpg)

#### Example 9
![1024×1536](docs/images/00391-2026-01-11-tempestByVlad_baseV01.jpg)

#### Example 10
![1152×1536](docs/images/00414-2026-01-11-tempestByVlad_baseV01.jpg)

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.

---

## Disclaimer

This repository documents observations and analysis of experimental Windows Insider Canary builds.  
It does not redistribute proprietary Microsoft software or claim ownership of Microsoft intellectual property.

All findings are presented for educational and research purposes only.

---

