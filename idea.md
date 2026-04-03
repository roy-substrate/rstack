# Hardware Device to Block Voice Recording Apps (Granola, Neosapiens, etc.)

## Problem Statement

AI meeting assistants — Granola, Neosapiens, Otter.ai, Fireflies.ai, and others — are increasingly recording conversations without meaningful consent from all participants. These tools operate invisibly: Granola captures system audio on your device with no bot joining the call, making it undetectable to other participants. There is a growing need for a hardware device that reliably prevents unauthorized voice recording in both physical and virtual meeting environments.

## How These Apps Capture Audio

| App | Method | Visibility |
|-----|--------|------------|
| **Granola** | System audio capture via OS permissions (macOS: Screen & System Audio Recording; Windows: microphone permission) | Invisible to other participants — no bot joins |
| **Otter.ai** | Joins call as a visible bot participant OR local capture | Visible bot OR invisible local |
| **Neosapiens** | Local audio capture + cloud transcription | Invisible |
| **Fireflies.ai** | Bot joins meeting OR browser extension | Visible bot or invisible extension |
| **Microsoft Copilot/Recall** | OS-level system audio capture | Built into OS |

**Key insight**: Software-based recording via system audio APIs (virtual audio drivers) is fundamentally different from physical microphone recording. A complete solution must address both vectors.

---

## Existing Solutions & Commercial Products

### 1. Deveillance Spectre I (Most Notable New Entry)

- **What**: Portable AI-powered device that scans surroundings, detects microphones, then emits signals to make captured audio unintelligible
- **Range**: ~2 meter protection zone around user
- **Price**: $1,199 (pre-order, 20% limited discount)
- **Ship date**: H2 2026
- **Founded by**: Harvard physics graduate Aida Baradari
- **Concerns**: Security researchers question RF-based microphone detection claims; no independent testing yet; very expensive

### 2. iSecus Audio Jammers (Established Brand)

- **AJS-333**: Integrated with Bluetooth speaker for concealment. Designed for 2-4 person meetings
- **AJB-331**: Cellphone jammer box (1 phone slot)
- **AJB-336**: High-end ultrasonic cellphone jammer box (4 phone slots) — physically blocks phone recording by enclosing devices
- **Method**: Ultrasonic emission targeting MEMS microphones

### 3. BlackPoint Security Devices

- **Portable ultrasonic jammer**: 6m range, 360° interference field
- **Silent tabletop jammer**: 8m range, 360° field, designed for boardrooms
- **Form factor**: Compact, battery-powered

### 4. Selcom Security SEL ULTRA SPEAKER

- **Form factor**: Disguised as ceiling speaker
- **Method**: High-quality ultrasonic transducers controlled by microprocessor
- **Use case**: Permanent installation in meeting rooms

### 5. JammerMFG YX-001-D

- **Method**: Speaker-type ultrasonic recording jammer
- **Claims**: Completely inaudible to human ears while disrupting recording

### 6. SpyAssociates Ultrasound Anti-Recording Device

- **Retail availability**: Consumer-grade, available on eBay and specialty retailers
- **Price range**: $100–$500 depending on model

### 7. DIY / Open Source: antispy-jammer (GitHub)

- **Repo**: mcore1976/antispy-jammer
- **Platform**: ATTINY13 / ATTINY85 / Arduino
- **Driver**: PAM8403 / TPA3116D2 module with piezo ultrasonic transducers
- **Cost**: ~$15-30 in parts
- **Limitation**: Lower power, smaller range than commercial devices

---

## The Science: How Ultrasonic Jamming Works

### Key Academic Research

**"Wearable Microphone Jamming"** — University of Chicago (CHI 2020)
- Authors: Yuxin Chen, Huiying Li, Shan-Yuan Teng, et al. (Ben Zhao & Haitao Zheng labs)
- **Breakthrough**: Wearable bracelet with 24 ultrasonic transducers jamming in all directions
- **Frequency**: White noise in 24–26 kHz range (inaudible to humans)
- **Mechanism**: Exploits nonlinear response of MEMS microphone circuits — ultrasonic noise "leaks" into audible range, drowning out speech
- **Innovation**: Natural hand/arm movement blurs jamming blind spots, increasing coverage
- **User study**: Participants rated privacy protection 5.4/7
- **Patent**: Filed by UChicago Polsky Center for Entrepreneurship

**"UltraJam: Ultrasonic adaptive jammer based on nonlinearity effect"** — ScienceDirect (2023)
- Adaptive ultrasonic signals that exploit microphone circuit nonlinearity
- Signals demodulate into low-frequency noise that masks speech

### The Physics

1. MEMS microphones (used in phones, laptops, smart speakers) have a nonlinear response to ultrasonic frequencies
2. When exposed to ultrasonic signals (20–26 kHz), the microphone circuit demodulates these into audible-range noise
3. This noise overwhelms the speech signal in the recording
4. Humans cannot hear the ultrasonic emission, so live conversation is unaffected
5. The effect is strongest when the jammer has line-of-sight to the microphone

### Limitations

- **Electret microphones** (older/professional) may be less susceptible than MEMS
- **Line-of-sight**: Ultrasonic waves are directional; obstacles block them
- **Software recording via system audio**: Ultrasonic jammers only affect physical microphones — they do NOT block software capturing audio from virtual audio drivers or system audio APIs
- **Adaptive filtering**: Advanced recording software could potentially filter out ultrasonic interference (arms race)

---

## Gap Analysis: What's Missing

### The Software Audio Capture Problem

**Critical gap**: None of the existing hardware jammers can block apps like Granola that capture audio at the OS/system level rather than through a physical microphone. When Granola captures system audio on macOS via Screen & System Audio Recording permission, no physical microphone is involved — the audio is intercepted digitally.

### Proposed Multi-Layer Solution

| Layer | Target | Approach |
|-------|--------|----------|
| **Hardware Layer 1** | Physical microphones in room | Ultrasonic jammer (24-26 kHz, 360°) |
| **Hardware Layer 2** | Phone/laptop microphones | Cellphone jammer box (like iSecus AJB-336) |
| **Software Layer 1** | System audio capture (macOS) | Revoke Screen & System Audio Recording permission; use virtual audio routing to inject noise |
| **Software Layer 2** | System audio capture (Windows) | Audio driver hook that adds imperceptible adversarial noise defeating ASR models |
| **Network Layer** | Cloud transcription APIs | DNS/firewall rules blocking transcription service endpoints |

### Ideal Hardware Device Spec

A next-generation device would combine:

1. **24-transducer ultrasonic array** (UChicago design) — 360° coverage, 24-26 kHz
2. **USB audio pass-through dongle** — sits between mic and computer, injects adversarial noise into the audio stream that defeats speech recognition but sounds like faint static to humans
3. **Network-level blocking** — companion software/router firmware that blocks known transcription API endpoints (Granola, Otter, Fireflies, Deepgram, AssemblyAI, etc.)
4. **Portable form factor** — tabletop puck or wearable bracelet
5. **Detection mode** — RF scanning or audio watermark detection to alert when recording is occurring

---

## OS-Level Blocking (Immediate, No Hardware Required)

### macOS
- **System Settings → Privacy & Security → Screen & System Audio Recording** → Remove/deny permission for Granola, Otter, etc.
- This is the single most effective software-level control

### Windows
- **Settings → Privacy & Security → Microphone** → Deny per-app microphone access
- System audio capture has no dedicated toggle — harder to block
- Third-party tools like **Windows Firewall** can block transcription API endpoints

### Both Platforms
- Use **Little Snitch** (macOS) or **GlassWire** (Windows) to block network connections to transcription services
- DNS-level blocking via Pi-hole or NextDNS for: `api.granola.ai`, `api.otter.ai`, `api.fireflies.ai`, etc.

---

## Legal Considerations

- **US**: Varies by state. Some states are one-party consent (only one person needs to consent to recording), others are all-party consent
- **EU/GDPR**: Recording without consent is generally prohibited; jammers may be legal for personal privacy protection
- **FCC**: Ultrasonic devices are generally legal as they operate above audio frequencies and below RF regulation thresholds. However, RF jammers (cell phone signal blockers) are **illegal** under US law (47 USC § 333)
- **Key distinction**: Ultrasonic audio jammers ≠ RF jammers. Ultrasonic jammers disrupt microphones acoustically, not electromagnetically, and are generally legal

---

## Recommended Products by Use Case

| Use Case | Recommended Product | Est. Cost |
|----------|-------------------|-----------|
| **Quick personal protection** | DIY antispy-jammer (Arduino) | $15-30 |
| **Executive meetings** | BlackPoint Silent Tabletop Jammer | $300-500 |
| **High-security boardroom** | Selcom SEL ULTRA SPEAKER (ceiling mount) + iSecus AJB-336 phone box | $1,000-3,000 |
| **Wearable/portable** | Deveillance Spectre I (when available) | $1,199 |
| **Software-only (free)** | Revoke OS permissions + DNS blocking | $0 |
| **Comprehensive** | Ultrasonic jammer + USB audio dongle + network blocking | $500-1,500 |

---

## Practical Recommendations by Threat Model

| Scenario | Best Approach |
|----------|--------------|
| Block bot-based tools (Otter, Fireflies) from your meetings | Meeting platform admin controls: block domains, require auth |
| Prevent Granola on **your own** machine | Revoke microphone + screen recording permissions in OS settings |
| Prevent Granola on **someone else's** machine recording your voice via their mic | Ultrasonic jammer in your room (affects their laptop mic if in range) |
| Prevent Granola on **someone else's** machine recording your voice via system audio | **Cannot be blocked by hardware** — your voice arrives as digital audio through the meeting platform. Only solution: don't speak, or use meeting platform controls to prevent recording |
| Conference room privacy from any hidden recording device | Ceiling-mounted ultrasonic jammer array (iSecus AJ-316D or Selcom Security) + sound masking system |
| Personal portable protection | Wearable ultrasonic jammer bracelet (DIY from UChicago design) or portable commercial unit |

---

## Key Academic Papers & Patents

1. **"Wearable Microphone Jamming"** — Chen, Li, Teng, Nagels, Li, Lopes, Zhao, Zheng (UChicago). CHI 2020. Seminal work: wearable bracelet jams 87%+ of words in all directions using ring-layout transducers.
2. **"Understanding the Effectiveness of Ultrasonic Microphone Jammer"** — arXiv:1904.08490 (2019). Explains MEMS microphone hardware non-linearity mechanism.
3. **"UltraJam: Ultrasonic adaptive jammer based on nonlinearity effect"** — ScienceDirect (2023). Adaptive signals demodulated by mic circuits into low-frequency jamming.
4. **"Dynamic Ultrasonic Jamming via Time-Frequency Mosaic"** — MDPI Electronics (2025). Frequency-coded jamming adapting to speaker voice characteristics.
5. **US Patent US20230131816A1** — "Wearable microphone jammer" (UChicago team). Covers bracelet form factor with ring-layout ultrasonic transducers.

---

## Known Limitations of Ultrasonic Jammers

- **Blind spots**: Destructive interference patterns mean no device has perfect 360° spherical coverage. UChicago bracelet drops from 97% to ~75% at 90° vertical angles.
- **Range**: Most effective within 1-6 meters only.
- **Adaptive noise cancellation**: Fixed-frequency jammers increasingly vulnerable to AI-based noise reduction. Newer adaptive/randomized jammers (UltraJam, TFM) attempt to counter this.
- **Device variability**: Different MEMS microphones (iPhone vs Android vs laptop) have different nonlinearity characteristics.
- **Does NOT block system audio capture**: If someone runs Granola capturing their system audio output (what their speakers play), a jammer in your room does not affect that digital audio path. It only jams physical microphones in range.
