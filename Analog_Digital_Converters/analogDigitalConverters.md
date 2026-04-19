

# ADC Parameters (Clean Reference Tables)

---

## 1. Fundamental Parameters

| Parameter | Details | Formula / Notes |
|----------|--------|----------------|
| Resolution | Bit depth, number of levels, dynamic range | Levels = 2^n |
| Least Significant Bit (LSB) Size | Step size, voltage per bit | LSB = Vref / 2^n |
| Full Scale Range (FSR) | Unipolar vs bipolar range | FSR = Vmax − Vmin |
| Reference Voltage (Vref) | Internal/external, stability, drift | Directly affects accuracy |
| Input Range | Matching with signal amplitude | Avoid clipping |

---

## 2. Timing Parameters

| Parameter | Details | Notes |
|----------|--------|------|
| Sampling Rate | Nyquist rate, oversampling | fs ≥ 2 × f_signal |
| Conversion Time | Time per sample | Depends on architecture |
| Throughput Rate | Samples per second | Includes delays |
| Aperture Time | Sampling instant duration | Affects high-frequency signals |
| Aperture Jitter | Timing uncertainty | Causes noise |
| Latency | Pipeline delay | Important in control systems |
| Acquisition Time | Time to charge sample capacitor | Impacts accuracy |

---

## 3. Accuracy Parameters

| Parameter | Details | Notes |
|----------|--------|------|
| Quantization Error | ±0.5 LSB uncertainty | Inherent |
| Offset Error | Shift from zero | Calibration needed |
| Gain Error | Scaling error | Affects slope |
| Integral Nonlinearity (INL) | Deviation from ideal curve | Measured in LSB |
| Differential Nonlinearity (DNL) | Step size variation | Missing codes if >1 LSB |
| Missing Codes | Skipped digital values | Due to DNL |
| Temperature Drift | Variation with temperature | Critical in precision systems |

---

## 4. Dynamic Performance

| Parameter | Details | Notes |
|----------|--------|------|
| Signal-to-Noise Ratio (SNR) | Noise vs signal ratio | Ideal ≈ 6.02n + 1.76 dB |
| Signal-to-Noise-and-Distortion ratio (SINAD) | Signal including distortion | Used for Modeling ADCs Using Effective-Number-of-Bits (ENOB) |
| Total-Harmonic -Distortion (THD) | Harmonic distortion | Nonlinearity indicator |
| Spurious-Free Dynamic Range (SFDR) | Spur-free dynamic range | Important in RF |
| Effective-Number-of-Bits (ENOB) | Effective number of bits | ENOB = (SINAD − 1.76)/6.02 |
| Noise Floor | Minimum detectable signal | Sets sensitivity |
| Dynamic Range | Max signal vs noise | In dB |

---

## 5. Electrical Parameters

| Parameter | Details | Notes |
|----------|--------|------|
| Input Voltage Range | Max/min allowable voltage | Avoid saturation |
| Input Impedance | Loading effect | High is better |
| Power Consumption | Static vs dynamic | Trade-off with speed |
| Supply Voltage | Analog/digital supply | Noise isolation |
| Common Mode Range | Differential input range | Stability factor |
| Leakage Current | Input bias effects | Affects precision |

---

## 6. Functional / Design Parameters

| Parameter | Details | Notes |
|----------|--------|------|
| **ADC Architecture**                   | Flash, SAR, Sigma-Delta, Pipeline                                     | Each offers trade-offs in speed, power, resolution, and complexity                     |
| Channels | Single vs multi-channel | Multiplexing |
| Input Type | Single-ended / differential | Noise immunity |
| Interface | SPI, I2C, parallel | Speed vs complexity |
| Sampling Method | Uniform / non-uniform | Mostly uniform |
| Anti-aliasing Filter | Pre-sampling filter | Prevents aliasing |
| Calibration | Internal/external | Improves accuracy |
| Clock Source | Internal/external | Jitter impact |
| **Aliasing & Anti-Aliasing**           | Nyquist criterion, anti-aliasing filters (low-pass), spectral folding | Input must be band-limited to avoid distortion; filters add cost and design complexity |
| **Oversampling & Decimation**          | Oversampling ratio (OSR), digital decimation filters                  | Improves resolution and noise performance; widely used in Sigma-Delta ADCs             |
| **Dithering Techniques**               | Additive noise, random vs shaped dither                               | Reduces quantization distortion; useful in audio and precision measurements            |
| **Speed vs Resolution Trade-off**      | Sampling rate vs number of bits                                       | Higher speed usually reduces achievable resolution due to noise and design limits      |
| **Power vs Accuracy Trade-off**        | Power consumption vs ENOB (Effective Number of Bits)                  | High-accuracy ADCs typically consume more power; critical in battery systems           |
| **Noise Sources**                      | Thermal noise, quantization noise, flicker (1/f) noise                | Sets lower bound on achievable resolution; impacts SNR and ENOB                        |
| **Quantization Noise**                 | Uniform noise model, step size error                                  | Inherent in digitization; reduced via oversampling and dithering                       |
| **Thermal Noise**                      | kT/C noise, resistor noise                                            | Dominant at high speeds and low signal levels                                          |
| **ADC Selection – Audio Systems**      | High resolution (16–24 bit), Sigma-Delta ADCs, low THD+N              | Prioritizes fidelity over speed; oversampling and filtering are essential              |
| **ADC Selection – Embedded Systems**   | SAR ADCs, moderate resolution (8–12 bit), low power                   | Balance of cost, speed, and efficiency; often integrated in microcontrollers           |
| **ADC Selection – Industrial Control** | High accuracy, robustness, isolation, moderate speed                  | Needs stability in noisy environments; often uses Sigma-Delta or high-precision SAR    |
| **Dynamic Range Considerations**       | SNR, ENOB, SFDR (Spurious-Free Dynamic Range)                         | Determines smallest and largest measurable signals                                     |
| **Linearity**                          | INL (Integral Nonlinearity), DNL (Differential Nonlinearity)          | Critical for precision applications like instrumentation                               |
| **Sampling Clock Effects**             | Jitter, phase noise                                                   | Clock imperfections degrade high-frequency signal accuracy                             |
| **Input Bandwidth**                    | Full-power bandwidth, small-signal bandwidth                          | Limits frequency range of accurate conversion                                          |
