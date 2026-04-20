Below is a **structured, engineering-style reference guide** covering your requested topics. I’ll keep it **defensive + educational (OEM/diagnostics/security research level)** and avoid unsafe step-by-step “attack enablement” instructions, while still giving you deep systems understanding.

---

# 1) Modern Vehicle CAN Architecture + Full Wiring Topology

## 1.1 High-level multi-CAN architecture (modern car)

| Network Layer             | Typical Speed | Function                    | ECUs               |
| ------------------------- | ------------- | --------------------------- | ------------------ |
| Powertrain CAN            | 500 kbps      | Engine + drivetrain control | ECM, TCM, ABS      |
| Chassis CAN               | 500 kbps      | Safety dynamics             | ABS, ESP, EPS      |
| Body CAN                  | 125–500 kbps  | Comfort systems             | BCM, doors, lights |
| Infotainment CAN          | 125 kbps      | Media/cluster               | IVI, cluster       |
| Diagnostic CAN (DoIP/OBD) | 500 kbps      | OBD/UDS access              | Gateway ECU        |

---

## 1.2 Physical CAN wiring diagram (typical OEM layout)

```
        [ECM]──┐
                │
        [TCM]───┤
                │
        [ABS]───┤==== CAN HIGH (Twisted Pair) ====┐
                │                                  │
        [BCM]───┤==== CAN LOW  (Twisted Pair) ====┘
                │
        [AIRBAG]┤
                │
         [EPS]──┘
              │
        [CENTRAL GATEWAY ECU]
              │
        [OBD-II PORT]
```

### CAN physical layer notes

| Parameter      | Value                                |
| -------------- | ------------------------------------ |
| Signal type    | Differential (CAN-H / CAN-L)         |
| Idle voltage   | ~2.5V both lines                     |
| Dominant state | CAN-H ↑ / CAN-L ↓                    |
| Termination    | 120Ω each end → ~60Ω total           |
| Cable          | Twisted pair (shielded in some OEMs) |

---

# 2) OBD-II Pin-Level CAN Mapping (ISO 15765-4)

## 2.1 Standard OBD-II connector (16-pin DLC)

| Pin | Signal                 |
| --- | ---------------------- |
| 4   | Chassis Ground         |
| 5   | Signal Ground          |
| 6   | CAN High (ISO 15765-4) |
| 14  | CAN Low (ISO 15765-4)  |
| 16  | Battery +12V           |

📌 Confirmed industry mapping: Pins **6 & 14 = High-Speed CAN bus** ([CarInterior][1])

---

## 2.2 OEM gateway routing through OBD

```
[Powertrain CAN] ─┐
[Chassis CAN]     ├──> [Gateway ECU] ───> OBD (filtered)
[Body CAN]        ┘
```

### Key idea:

OBD does NOT expose full CAN
➡ Gateway filters traffic (very important in modern vehicles)

---

# 3) ECU Gateway Routing Logic (Modern Architecture)

## 3.1 Why gateways exist

Modern cars use **segmentation + security isolation**:

| Reason            | Explanation                       |
| ----------------- | --------------------------------- |
| Safety            | Prevent brake/engine interference |
| Security          | Block unauthorized injection      |
| Bandwidth control | Reduce bus load                   |
| OEM separation    | Different ECU domains             |

---

## 3.2 Gateway routing table (simplified)

| Source CAN                | Destination CAN | Allowed?            | Filter rule |
| ------------------------- | --------------- | ------------------- | ----------- |
| Powertrain → Chassis      | Yes             | Allowed IDs only    |             |
| Infotainment → Powertrain | No              | Blocked             |             |
| OBD → Powertrain          | Limited         | Diagnostic IDs only |             |
| Body → Cluster            | Yes             | Whitelist signals   |             |

---

## 3.3 Routing logic pseudocode

```c
if(frame.source == OBD_PORT)
{
    if(frame.id in DIAGNOSTIC_RANGE)
        forward_to_target_ecu(frame);
    else
        drop_frame();
}

if(frame.source == infotainment_can)
{
    if(frame.target == powertrain_can)
        deny();
}
```

---

## 3.4 Advanced gateway features (modern cars)

| Feature                      | Description            |
| ---------------------------- | ---------------------- |
| Message firewall             | ID filtering           |
| Signal whitelisting          | Only allowed PIDs      |
| Rate limiting                | Prevent flooding       |
| Secure onboard comms (SecOC) | Message authentication |
| Intrusion detection          | CAN anomaly detection  |

---

# 4) Oscilloscope CAN Waveform Guide (Interpretation)

## 4.1 Standard CAN waveform

### Idle state

```
CAN-H = ~2.5V
CAN-L = ~2.5V
```

### Dominant bit (logic 0)

```
CAN-H = 3.5V
CAN-L = 1.5V
Differential = ~2V
```

---

## 4.2 Oscilloscope pattern view

```
CAN-H:  _-__-_-___-_-_
CAN-L:  -_--_-___-_-__
          (inverse mirror)
```

---

## 4.3 Common waveform diagnostics

| Fault                 | Waveform signature        |
| --------------------- | ------------------------- |
| Short CAN-H to GND    | Flat low line             |
| Short CAN-L to GND    | Distorted recessive level |
| Open termination      | Reflection noise          |
| Missing 120Ω resistor | ~120Ω instead of 60Ω      |
| EMI noise             | jitter + spikes           |

---

## 4.4 Bit timing reference (500 kbps CAN)

| Parameter | Value              |
| --------- | ------------------ |
| Bit time  | 2 µs               |
| Recessive | both lines ~2.5V   |
| Dominant  | differential shift |

---

# 5) ABS ECU Fault / Short Circuit Simulation (Training Scenario)

⚠️ This is **training simulation logic only (no real wiring instructions)**

---

## 5.1 ABS ECU CAN role

| Function         | Description       |
| ---------------- | ----------------- |
| Wheel speed data | Sends to ECM      |
| Brake pressure   | Stability control |
| ESP coordination | Vehicle dynamics  |

---

## 5.2 Simulation scenario table

| Step | Condition injected       | Expected CAN behavior     |
| ---- | ------------------------ | ------------------------- |
| 1    | Normal ABS comms         | Wheel speed frames stable |
| 2    | Sensor dropout (virtual) | ABS flags DTC             |
| 3    | CAN timeout              | Gateway logs fault        |
| 4    | Message corruption       | CRC mismatch detected     |
| 5    | ECU isolation            | ABS enters fallback mode  |

---

## 5.3 Fault response hierarchy

```
Fault detected
   ↓
ECU self-check
   ↓
Send DTC to gateway
   ↓
Disable stability functions (partial)
   ↓
ABS fallback braking mode
```

---

## 5.4 Typical DTC categories

| Type     | Example                   |
| -------- | ------------------------- |
| C-series | Chassis faults (ABS, ESP) |
| U-series | Network communication     |
| P-series | Powertrain faults         |

---

# 6) CAN Bus Security & Automotive Cybersecurity Overview

## 6.1 Core vulnerability model

CAN was designed in 1980s → **no authentication by default**

| Weakness             | Impact                     |
| -------------------- | -------------------------- |
| No encryption        | Sniffing possible          |
| No authentication    | Spoofing possible          |
| Broadcast model      | All ECUs see frames        |
| Priority arbitration | Denial-of-service possible |

---

## 6.2 Attack surface (high-level)

| Layer      | Example risk        |
| ---------- | ------------------- |
| Physical   | OBD access          |
| Network    | CAN injection       |
| ECU        | firmware compromise |
| Gateway    | rule bypass         |
| Telematics | remote intrusion    |

---

## 6.3 Common CAN attack classes

| Attack type    | Description                     |
| -------------- | ------------------------------- |
| Replay attack  | resend valid frames             |
| Spoofing       | fake ECU identity               |
| DoS flood      | bus saturation                  |
| Signal fuzzing | malformed frames                |
| ECU takeover   | compromised node sends messages |

---

## 6.4 Defensive architecture in modern cars

| Defense          | Method                       |
| ---------------- | ---------------------------- |
| SecOC            | message authentication       |
| IDS ECU          | anomaly detection            |
| Gateway firewall | filtering                    |
| Rolling counters | prevent replay               |
| HSM modules      | cryptographic ECU protection |

---

## 6.5 Security evolution timeline

| Era       | Security level                           |
| --------- | ---------------------------------------- |
| Pre-2005  | None                                     |
| 2005–2015 | Basic gateway filtering                  |
| 2015–2020 | Secure diagnostics (UDS, SecOC adoption) |
| 2020+     | IDS + cryptographic CAN                  |

---
