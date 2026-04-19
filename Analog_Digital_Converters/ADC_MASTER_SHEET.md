# 📊 ADC Master Sheet (Complete Guide)

---

# 📘 1. Basic Concepts

| No. | Topic | Concept | Key Parameters | Formula / Notes |
|-----|------|--------|----------------|-----------------|
| 1 | What is an ADC? | Analog → Digital conversion | Resolution, Vref | Converts continuous signal to discrete |
| 2 | Analog vs Digital signals | Signal types | Amplitude, time | Analog = continuous |
| 3 | Resolution | Number of discrete levels | Bits (n) | Levels = 2ⁿ |
| 4 | LSB | Smallest step size | Vref, n | LSB = Vref / 2ⁿ |
| 5 | Quantization | Rounding process | LSB | Error unavoidable |
| 6 | Quantization error | Difference from actual value | ±0.5 LSB | Uniform error |
| 7 | Sampling | Discrete time capture | Sampling rate (fs) | — |
| 8 | Nyquist theorem | Minimum sampling condition | fs, signal freq | fs ≥ 2f |
| 9 | Aliasing | Undersampling effect | fs | Signal distortion |
| 10 | Full-scale range | Input limits | Vmax, Vmin | FSR = Vmax − Vmin |

---

# ⚙️ 2. Core Parameters

| No. | Parameter | Concept | Key Idea |
|-----|----------|--------|----------|
| 11 | Resolution | Precision | Higher bits → better accuracy |
| 12 | LSB size | Step size | Vref / 2ⁿ |
| 13 | Reference voltage | Scaling | Stability critical |
| 14 | Input range | Signal bounds | Avoid clipping |
| 15 | Sampling rate | Speed | Must satisfy Nyquist |
| 16 | Conversion time | Delay per sample | Depends on ADC type |
| 17 | Throughput | Samples/sec | Includes overhead |
| 18 | Aperture time | Sampling instant | Impacts high-frequency accuracy |
| 19 | Latency | Output delay | Pipeline dependent |
| 20 | Acquisition time | Input settling time | Important in SAR ADC |
| 21 | Offset error | Zero shift | Requires calibration |
| 22 | Gain error | Scaling error | Affects slope |
| 23 | INL | Linearity error | Deviation from ideal |
| 24 | DNL | Step variation | Missing codes possible |
| 25 | Quantization error | Rounding error | ±0.5 LSB |

---

# 🔊 3. Dynamic Performance

| No. | Parameter | Description | Formula / Notes |
|-----|----------|-------------|-----------------|
| 26 | SNR | Signal-to-noise ratio | ≈ 6.02n + 1.76 |
| 27 | SINAD | Signal + noise + distortion | Used for ENOB |
| 28 | THD | Harmonic distortion | Non-linearity measure |
| 29 | SFDR | Spur-free dynamic range | RF applications |
| 30 | ENOB | Effective bits | (SINAD − 1.76) / 6.02 |
| 31 | Noise floor | Minimum detectable signal | dB |
| 32 | Dynamic range | Max/min signal ratio | Wide = better |

---

# ⚡ 4. Electrical Parameters

| No. | Parameter | Concept |
|-----|----------|--------|
| 33 | Input voltage | Signal range |
| 34 | Input impedance | Loading effect |
| 35 | Power consumption | Energy usage |
| 36 | Supply voltage | Operating range |
| 37 | Common-mode range | Differential stability |
| 38 | Leakage current | Input bias error |

---

# 🔁 5. Design & Architecture

| No. | Topic | Concept | Notes |
|-----|------|--------|------|
| 39 | ADC types | Architectures | Flash, SAR, Sigma-Delta |
| 40 | Channels | Input count | Multiplexing possible |
| 41 | Input type | Signal format | Single-ended / Differential |
| 42 | Interface | Communication | SPI, I2C |
| 43 | Clock source | Timing | Internal / external |
| 44 | Anti-alias filter | Pre-filtering | Removes high freq noise |
| 45 | Calibration | Error correction | Improves accuracy |

---

# 🚀 6. Advanced Topics

| No. | Topic | Concept | Notes |
|-----|------|--------|------|
| 46 | Aliasing | Frequency folding | fs < 2f causes distortion |
| 47 | Anti-aliasing | LPF design | fc ≈ fs/2 |
| 48 | Oversampling | Higher sampling rate | Improves SNR |
| 49 | Decimation | Downsampling | Reduces data rate |
| 50 | Dithering | Noise addition | Improves linearity |
| 51 | Speed vs resolution | Trade-off | Faster → lower resolution |
| 52 | Power vs accuracy | Trade-off | Higher ENOB → more power |
| 53 | Thermal noise | kT/C noise | Temperature dependent |
| 54 | Quantization noise | Digital error | Δ²/12 |
| 55 | Audio ADC | Application | 16–24 bit, high SNR |
| 56 | Embedded ADC | MCU systems | Balanced performance |
| 57 | Industrial ADC | Precision systems | High stability required |

---

# 📌 Notes

- Higher resolution increases accuracy but reduces speed.
- Nyquist criterion is essential to avoid aliasing.
- ENOB is more practical than ideal resolution.
- SAR ADCs are most commonly used in embedded systems.

---

# 📂 Suggested Extensions

You can extend this sheet with:
- Worked numerical examples
- ADC architecture diagrams (Flash, SAR, Sigma-Delta)
- Interview Q&A section
- Python simulations of quantization & sampling
