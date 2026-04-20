Below is an **in-depth, structured reference** on CAN bus fundamentals, waveforms, architecture, diagnostics, and fault cases in **table format**.

---

# 1. CAN High vs CAN Low Waveforms (Physical Layer Behavior)

| Parameter                 | CAN High (CAN_H) | CAN Low (CAN_L) | Explanation                                             |
| ------------------------- | ---------------- | --------------- | ------------------------------------------------------- |
| Recessive State (logic 1) | ~2.5 V           | ~2.5 V          | Both lines equal → no differential voltage → bus idle   |
| Dominant State (logic 0)  | ~3.5 V           | ~1.5 V          | Differential voltage ≈ 2 V → active transmission        |
| Idle Voltage              | ~2.5 V           | ~2.5 V          | Bus termination holds mid-level bias                    |
| Signal Type               | Differential     | Differential    | Noise immunity achieved via voltage difference          |
| Bit Dominance             | Higher voltage   | Lower voltage   | CAN uses wired-AND logic (dominant overrides recessive) |
| Noise Immunity            | High             | High            | External noise cancels due to differential pairing      |

### Key Insight

CAN does NOT rely on absolute voltage, but on:

> **Vdiff = CAN_H − CAN_L**

| State     | CAN_H | CAN_L | Differential |
| --------- | ----- | ----- | ------------ |
| Recessive | 2.5 V | 2.5 V | 0 V          |
| Dominant  | 3.5 V | 1.5 V | 2.0 V        |

---

# 2. CAN Waveform Behavior (Oscilloscope View)

| Condition        | CAN_H Waveform          | CAN_L Waveform          | Interpretation                |
| ---------------- | ----------------------- | ----------------------- | ----------------------------- |
| Normal Idle      | Flat ~2.5 V             | Flat ~2.5 V             | No communication              |
| Normal Data      | Square wave 2.5 → 3.5 V | Square wave 2.5 → 1.5 V | Proper differential signaling |
| Heavy Traffic    | Dense pulse train       | Dense inverse pulses    | High bus utilization          |
| Weak Termination | Rounded edges           | Rounded edges           | Reflections on bus            |
| EMI Noise        | Slight distortion       | Slight distortion       | Usually still readable        |

---

# 3. Real Car CAN Network Architecture (ECU Layout)

Modern vehicles use **multi-CAN networks interconnected via gateways**.

| Network Type                  | Speed             | Purpose                       | Typical ECUs       |
| ----------------------------- | ----------------- | ----------------------------- | ------------------ |
| Powertrain CAN                | 500 kbps – 1 Mbps | Engine & transmission control | ECU, TCU, ABS      |
| Body CAN                      | 125 – 500 kbps    | Comfort systems               | BCM, doors, lights |
| Diagnostic CAN (OBD-II)       | 500 kbps          | External scan tools           | Gateway ECU        |
| Infotainment CAN              | 125 kbps          | Multimedia systems            | Head unit, display |
| Safety CAN (sometimes CAN FD) | 500 kbps – 2 Mbps | ADAS systems                  | Radar, camera ECUs |

---

## Simplified Vehicle CAN Architecture

| Layer                    | Description                          |
| ------------------------ | ------------------------------------ |
| Sensor Layer             | Wheel speed, temp sensors, etc.      |
| ECU Layer                | ABS, Engine, Airbag, BCM             |
| Sub-network CANs         | Multiple isolated CAN buses          |
| Gateway ECU              | Routes messages between CAN networks |
| Diagnostic Port (OBD-II) | External access point                |

### Architecture Diagram (Text View)

```
        [Engine ECU] ---- CAN Powertrain ---- [TCU]
               |                                 |
               |                                 |
        [ABS ECU] -------------------------------|
               |
            [Gateway ECU]
               |
    -----------------------------
    |            |              |
Body CAN     Infotainment   Diagnostic (OBD-II)
(BCM, lights)   (Audio)        Scanner access
```

---

# 4. Diagnostic Approach Using OBD Scanner

| Step | Tool Action                         | Expected Result               | Interpretation                           |
| ---- | ----------------------------------- | ----------------------------- | ---------------------------------------- |
| 1    | Connect OBD-II scanner              | ECU communication established | Bus is alive                             |
| 2    | Read DTCs                           | U-codes / P-codes             | Network or ECU fault                     |
| 3    | Check live data                     | RPM, speed, sensors           | Confirms ECU data flow                   |
| 4    | Network scan                        | List of ECUs                  | Missing ECU = communication issue        |
| 5    | CAN status test (advanced scanners) | Bus load %, errors            | Detects congestion or fault              |
| 6    | Ping ECU (diagnostic request)       | Response time                 | Slow/no response = wiring or ECU failure |

---

## Common OBD-II Network Fault Codes

| Code  | Meaning                              |
| ----- | ------------------------------------ |
| U0100 | Lost communication with ECM          |
| U0140 | Lost communication with BCM          |
| U0121 | Lost communication with ABS module   |
| U0001 | High-speed CAN fault                 |
| U0073 | Control module communication bus off |

---

# 5. CAN Fault Cases (Electrical & Logical Failures)

## A. Open Circuit Faults

| Fault Type      | Effect on CAN_H  | Effect on CAN_L  | Symptoms                               | Diagnosis                 |
| --------------- | ---------------- | ---------------- | -------------------------------------- | ------------------------- |
| CAN_H wire open | Floating voltage | Normal 2.5 V     | No communication / intermittent faults | Check continuity of CAN_H |
| CAN_L wire open | Normal 2.5 V     | Floating voltage | ECU dropouts                           | Resistance test           |
| Both open       | Floating         | Floating         | Complete network failure               | Open circuit test         |

---

## B. Short Circuit Faults

| Fault Type           | CAN_H         | CAN_L         | System Behavior        | Outcome                     |
| -------------------- | ------------- | ------------- | ---------------------- | --------------------------- |
| CAN_H short to GND   | 0 V           | 2.5 V         | Bus collapses          | No communication            |
| CAN_L short to GND   | 2.5 V         | 0 V           | Bus error frames       | ECU resets                  |
| CAN_H short to VCC   | 5 V           | 2.5 V         | Overvoltage            | ECU damage risk             |
| CAN_L short to VCC   | 2.5 V         | 5 V           | Severe mismatch        | Bus failure                 |
| CAN_H short to CAN_L | Equal voltage | Equal voltage | No differential signal | Complete communication loss |

---

## C. Termination Faults (Very Common)

| Condition                             | Result             | Symptom                    |
| ------------------------------------- | ------------------ | -------------------------- |
| Missing 120Ω resistor                 | Signal reflections | Random DTCs                |
| Double termination (>120Ω equivalent) | Weak signal        | Intermittent communication |
| No termination                        | Severe noise       | Bus error, no ECU response |

### Normal CAN resistance (key diagnostic check)

| Measurement between CAN_H and CAN_L | Expected            |
| ----------------------------------- | ------------------- |
| Ignition OFF                        | ~60 Ω               |
| (Two 120Ω resistors in parallel)    | Correct termination |

---

## D. Signal Quality Faults

| Fault                   | Cause                      | Effect              |
| ----------------------- | -------------------------- | ------------------- |
| EMI interference        | Alternator, ignition coils | Bit errors          |
| Corrosion in connectors | Moisture ingress           | Intermittent faults |
| Poor grounding          | Voltage offset             | Unstable CAN levels |
| Water damage            | Harness shorting           | Random ECU resets   |

---

# 6. Practical Diagnostic Workflow (Technician Method)

| Step | Action                     | Tool             | Result Interpretation       |
| ---- | -------------------------- | ---------------- | --------------------------- |
| 1    | Check DTCs                 | OBD scanner      | Identify affected network   |
| 2    | Measure CAN resistance     | Multimeter       | ~60Ω = healthy              |
| 3    | Check CAN voltage          | Multimeter       | ~2.5V idle                  |
| 4    | Oscilloscope check         | Scope            | Validate waveform integrity |
| 5    | Disconnect ECUs one by one | Manual isolation | Find shorted module         |
| 6    | Harness continuity test    | Multimeter       | Identify open circuits      |

---

# 7. Key Diagnostic Voltage Patterns (Quick Reference)

| Condition           | CAN_H     | CAN_L     | Interpretation      |
| ------------------- | --------- | --------- | ------------------- |
| Healthy bus         | 2.5–3.5 V | 2.5–1.5 V | Normal operation    |
| Short to ground     | 0 V       | ~2.5 V    | Fault detected      |
| Short to battery    | ~5 V      | abnormal  | ECU risk            |
| Both stuck at 2.5 V | 2.5 V     | 2.5 V     | Bus idle or failure |

---

# 8. Important Engineering Insight

CAN bus reliability is based on three principles:

| Principle                 | Explanation             |
| ------------------------- | ----------------------- |
| Differential signaling    | Noise cancellation      |
| Deterministic arbitration | Message priority system |
| Redundant termination     | Signal stability        |

---
