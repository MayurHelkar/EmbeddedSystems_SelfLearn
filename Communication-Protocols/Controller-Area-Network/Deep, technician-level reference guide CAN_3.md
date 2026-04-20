Below is a **structured OEM/automotive engineering deep-dive** covering all four areas you listed. I’ll keep it aligned with **AUTOSAR / ISO 14229 / ISO 15765-2 / real ECU architecture practices**, focusing on **how systems are designed and validated (defensive + engineering view)** rather than exploitation.

---

# 1. AUTOSAR CAN Stack Architecture (Classic AUTOSAR)

## 1.1 Layered communication stack (bottom → top)

```
+------------------------------+
| Application Software (SWC)   |
+------------------------------+
| RTE (Run-Time Environment)   |
+------------------------------+
| COM (Signal packing layer)   |
+------------------------------+
| PDU Router (routing layer)   |
+------------------------------+
| CAN Interface (CanIf)        |
+------------------------------+
| CAN Driver                  |
+------------------------------+
| CAN Controller (HW)         |
+------------------------------+
| Physical CAN Bus             |
+------------------------------+
```

---

## 1.2 Role of each layer (OEM interpretation)

| Layer      | Function             | Key responsibility       |
| ---------- | -------------------- | ------------------------ |
| SWC        | Vehicle logic        | Torque, braking, sensors |
| RTE        | Glue layer           | SWC ↔ COM communication  |
| COM        | Signal packing       | Signals → frames         |
| PDU Router | Routing engine       | Which bus gets message   |
| CanIf      | Hardware abstraction | Frame TX/RX control      |
| Driver     | Low-level control    | Registers, interrupts    |

---

## 1.3 COM → PDU flow (important concept)

### Signal-level representation

| Signal        | Example    |
| ------------- | ---------- |
| WheelSpeed_FL | 0–250 km/h |
| BrakePressure | 0–120 bar  |

### COM packing process

```
Signals → Signal Group → I-PDU → CAN Frame
```

### Example mapping

| Signal        | Byte     | Bit    |
| ------------- | -------- | ------ |
| WheelSpeed_FL | Byte 0–1 | 16-bit |
| BrakePressure | Byte 2   | 8-bit  |

---

## 1.4 PDU Router logic (core OEM concept)

```c id="pdu_router"
if(PDU_ID == 0x123)
    route_to(CAN_POWERTRAIN);

else if(PDU_ID == 0x456)
    route_to(CAN_CHASSIS);

else
    drop();
```

---

## 1.5 Key AUTOSAR concept: I-PDU vs N-PDU

| Type  | Meaning                         |
| ----- | ------------------------------- |
| I-PDU | Internal AUTOSAR representation |
| N-PDU | Network frame (CAN/ETH/FlexRay) |

---

# 2. UDS Diagnostic Session Flows (ISO 14229)

UDS = Unified Diagnostic Services

---

## 2.1 Typical diagnostic stack

```
Tester (Scan Tool)
   ↓
DoIP / CAN Transport (ISO-TP)
   ↓
UDS Server (ECU)
```

---

## 2.2 Session control (0x10 service)

| Sub-function | Meaning                     |
| ------------ | --------------------------- |
| 0x01         | Default session             |
| 0x02         | Programming session         |
| 0x03         | Extended diagnostic session |

### Flow example

| Step | Request | Response                 |
| ---- | ------- | ------------------------ |
| 1    | 10 03   | Request extended session |
| 2    | ECU     | 50 03 + P2 timing        |

---

## 2.3 Read Data By Identifier (0x22)

### Structure

```
Request:  22 F1 90
Response:  62 F1 90 XX XX
```

| Field  | Meaning           |
| ------ | ----------------- |
| 0xF190 | VIN               |
| 0x62   | Positive response |

---

## 2.4 Write Data By Identifier (0x2E)

### Flow

| Step | Action                |
| ---- | --------------------- |
| 1    | Enter security access |
| 2    | Send 2E DID + data    |
| 3    | ECU writes memory     |
| 4    | Response 6E           |

---

## 2.5 Security Access (critical concept)

| Step         | Function |
| ------------ | -------- |
| Seed request | 27 01    |
| Key response | 27 02    |

ECU uses:

* rolling algorithm
* HSM-based crypto (modern ECUs)

---

## 2.6 ISO-TP segmentation (important)

Large UDS message:

| Frame type        | Meaning           |
| ----------------- | ----------------- |
| Single Frame      | ≤7 bytes          |
| First Frame       | start multi-frame |
| Consecutive Frame | continuation      |
| Flow Control      | pacing            |

---

# 3. Gateway Firewall Rule Engineering (OEM Style)

Modern gateways are **central security + routing enforcement ECUs**

---

## 3.1 Gateway architecture

```
[CAN Powertrain]
        |
        | → Gateway ECU → rule engine → filtering
        |
[CAN Body]  [CAN Chassis]  [Infotainment]
```

---

## 3.2 Rule model (OEM style abstraction)

Each message is evaluated by:

| Parameter     | Example          |
| ------------- | ---------------- |
| CAN ID        | 0x1F0            |
| Source ECU    | ABS              |
| Target domain | Powertrain       |
| Frequency     | 10 ms            |
| Allowed state | ignition ON only |

---

## 3.3 Firewall rule table (example OEM design)

| Rule ID | Condition                 | Action        |
| ------- | ------------------------- | ------------- |
| R1      | Infotainment → Powertrain | DROP          |
| R2      | Diagnostic session active | ALLOW UDS IDs |
| R3      | CAN ID not in whitelist   | DROP          |
| R4      | Frame rate > threshold    | RATE LIMIT    |
| R5      | Invalid CRC pattern       | LOG + DROP    |

---

## 3.4 Pseudologic (gateway core)

```c id="gateway_fw"
if(!is_whitelisted(CAN_ID))
    drop(frame);

if(source == INFOTAINMENT && dest == POWERTRAIN)
    deny();

if(rate(CAN_ID) > threshold)
    throttle();
```

---

## 3.5 Advanced OEM protections

| Feature                  | Purpose             |
| ------------------------ | ------------------- |
| Signal-level firewall    | block unsafe values |
| Timing anomaly detection | detect spoofing     |
| Rolling counter check    | prevent replay      |
| Secure gateway partition | isolate domains     |

---

# 4. Oscilloscope CAN Decoding (Bit Stuffing + ISO-TP)

---

## 4.1 CAN physical waveform recap

| State     | CAN-H | CAN-L |
| --------- | ----- | ----- |
| Recessive | 2.5V  | 2.5V  |
| Dominant  | 3.5V  | 1.5V  |

---

## 4.2 Bit stuffing rule (very important)

### Rule:

After **5 consecutive identical bits → insert opposite bit**

| Pattern | Result |
| ------- | ------ |
| 11111   | 111110 |
| 00000   | 000001 |

---

## 4.3 Why bit stuffing exists

| Reason          | Benefit          |
| --------------- | ---------------- |
| Clock recovery  | keeps sync       |
| Prevent DC bias | signal integrity |
| Maintain edges  | stable decoding  |

---

## 4.4 Oscilloscope interpretation flow

```
Analog waveform
   ↓
Differential decoding
   ↓
Bit timing extraction
   ↓
Bit de-stuffing
   ↓
Frame reconstruction
```

---

## 4.5 ISO-TP reassembly example

### Multi-frame message

| Frame | Content             |
| ----- | ------------------- |
| FF    | length + start data |
| CF1   | continuation        |
| CF2   | continuation        |

---

### Example flow

```
FF: 10 14 ...
CF: 21 ...
CF: 22 ...
CF: 23 ...
```

Final reassembled payload:

```
Vehicle diagnostic data block (20 bytes)
```

---

## 4.6 Real oscilloscope interpretation checklist

| Observation               | Meaning            |
| ------------------------- | ------------------ |
| clean differential signal | healthy bus        |
| ringing edges             | impedance mismatch |
| asymmetry                 | wiring fault       |
| noise spikes              | EMI interference   |
| flat line                 | bus short/open     |

---

# 5. Secure vs Insecure ECU Design Comparison

---

## 5.1 Architecture comparison

| Feature     | Insecure ECU  | Secure ECU       |
| ----------- | ------------- | ---------------- |
| CAN auth    | None          | SecOC / MAC      |
| Firmware    | Unprotected   | Signed           |
| Debug ports | Open          | Locked / fused   |
| Gateway     | simple filter | security gateway |
| UDS access  | unrestricted  | role-based       |

---

## 5.2 Attack surface comparison

| Layer               | Insecure ECU        | Secure ECU               |
| ------------------- | ------------------- | ------------------------ |
| OBD access          | full CAN visibility | filtered                 |
| diagnostic services | open UDS            | seed-key + roles         |
| firmware update     | unsigned            | cryptographically signed |
| message injection   | accepted            | rejected if invalid MAC  |

---

## 5.3 Secure ECU internal architecture

```
CAN RX
  ↓
Gateway Filter
  ↓
SecOC Verification (MAC check)
  ↓
PDU Router
  ↓
Application SWC
```

---

## 5.4 Key security modules

| Module            | Function                  |
| ----------------- | ------------------------- |
| HSM               | cryptographic key storage |
| IDS ECU           | anomaly detection         |
| Secure Boot       | firmware integrity        |
| Secure Diagnostic | authentication gating     |

---

## 5.5 Practical industry reality

| Area                | Reality                  |
| ------------------- | ------------------------ |
| Legacy vehicles     | weak/no authentication   |
| Modern premium OEMs | strong gateway + SecOC   |
| Budget vehicles     | partial protection       |
| EV platforms        | strongest security stack |

---
