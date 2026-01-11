# Workstation Findings and Validation
## Windows 11 Canary 25H2 (Build 26200.x)

---

## Scope

This document summarizes the verified findings collected from a Windows 11 Insider Canary workstation operating on version 25H2 (Build 26200.x).

It documents:
- What was observed
- What was validated
- What was ruled out
- Why the observed behavior matters

This document does not repeat Canary background or research sources, which are covered elsewhere in this repository.

---

## System Context

The findings documented here were collected on a high-end AI workstation configured for advanced GPU workloads, including:

- Windows 11 Insider Canary, version 25H2 (Build 26200.x)
- Hybrid GPU configuration
- WSL2 with GPU passthrough enabled
- Long-lived CUDA compute workloads
- High VRAM utilization
- Frequent transitions between Linux GPU compute and Windows desktop usage

This workload profile is atypical for most Canary systems.

---

## Summary of Observed Behavior

Following sustained GPU compute activity, the workstation exhibited a repeatable failure in the Windows imaging pipeline:

- Screenshot files were created successfully on disk
- Image rendering failed across Paint, Photos, and Explorer
- Snipping Tool crashed immediately after capture
- The behavior reproduced across all user profiles
- Reboots did not restore functionality

The failure consistently manifested during transitions from GPU compute back to Windows desktop rendering.

---

## Validation Performed

The following validation steps were completed and recorded:

### System Integrity
- SFC verification completed successfully with no integrity violations
- DISM component store checks reported a healthy system

### Application Integrity
- Paint, Photos, and Snipping Tool packages were installed and current
- AppX re-registration did not alter behavior
- The issue was not scoped to a single user profile

### Media and Imaging Stack
- Media Foundation binaries were present and versioned
- Windows Imaging Component binaries were present and versioned
- Media playback features were enabled

### GPU and Compute State
- GPU compute remained functional
- WSL2 remained healthy and operational
- High VRAM and BAR1 utilization was observed prior to failure
- Desktop Window Manager remained active

### Capture Path Behavior
- Native Windows capture tools failed
- Third-party software capture tools failed
- This indicates a failure below the application layer, within desktop composition or imaging surfaces

---

## What Was Ruled Out

Based on the above validation, the following were ruled out:

- OS corruption
- Missing or disabled Windows features
- AppX package corruption
- User profile corruption
- Driver installation failure
- Single-application defects

The failure pattern is not consistent with misconfiguration or partial installation.

---

## Interpretation

The collected evidence indicates a runtime regression in the Windows imaging and desktop composition pipeline that is activated under advanced GPU workload transitions.

This behavior aligns with:
- Canary channel experimentation
- Controlled Feature Rollout dynamics
- Platform-level changes affecting media and composition paths

Once activated, the failure is not recoverable through local repair mechanisms, which is consistent with documented Canary behavior.

---

## Relationship to Canary Documentation

These findings are consistent with Microsoft’s documented Canary and CFR model:

- Static health checks do not reflect runtime CFR state
- Experimental code paths can be activated dynamically
- Regressions may surface only under specific workload patterns
- Resolution typically requires a future Canary update or re-baselining

---

## Status

The findings documented here represent a point-in-time snapshot.

Further updates will record:
- Behavior changes across subsequent Canary cumulative updates
- Results of controlled re-baselining
- Additional correlation with GPU and media subsystem behavior

---

## Notes on Evidence Handling

Raw logs and diagnostic outputs were collected during this investigation.  
To keep this repository readable and focused, findings are summarized here rather than embedding large log files directly.

Supporting artifacts may be added selectively if they provide additional clarity.

---

