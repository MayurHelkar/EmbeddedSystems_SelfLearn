#1. Introduction to Microcontrollers

| Type  | Questions                                                                                                                                 |        |        |        |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------ | ------ | ------ |
|       | 🟡 Basic | 🟢 Intermediate | 🔵 Advanced | 🔴 Expert |
| What  | What is a microcontroller?<br> What are its main components?<br> What is the difference between a microcontroller and a microprocessor?   | What is firmware? | What is real-time constraint in microcontrollers? | What defines a hard vs soft real-time system? |
| Why   | Why are microcontrollers used instead of general-purpose computers in embedded systems?<br> Why are they preferred in low-power applications?<br> Why is it called an embedded system? | Why are microcontrollers optimized for specific tasks? | Why is determinism more important than speed? | Why can adding features break real-time guarantees? |
| How   | How does a microcontroller execute a program?<br> How is it programmed and debugged?<br> How is a microcontroller different from a computer? | How does a microcontroller boot up? | How does architecture affect execution predictability? | How would you architect a fail-safe embedded system? |
| Where | Where are microcontrollers commonly used (home appliances, automotive, IoT)?<br> Where are microcontrollers used in daily life? | Where does embedded firmware reside? | Where do embedded systems fail under load? | Where do timing violations typically originate? |
| When  | When should you choose a microcontroller over a microprocessor or FPGA?<br> When do we prefer microcontrollers over PCs? | When do you upgrade firmware? | When does system latency become critical? | When should you migrate from MCU to RTOS/Linux? |
| Who   | Who designs microcontrollers?<br> Who typically programs them (engineers, hobbyists)?<br> Who uses microcontrollers (industries, roles)? | Who writes firmware vs hardware design? | Who defines timing constraints in a project? | Who owns system-level reliability decisions? |

-----

#2. CPU (Central Processing Unit)

| Type  | Questions                                                                     |      |     |      |
| ----- | ----------------------------------------------------------------------------- | ---- |---- | ---- |
|       | 🟡 Basic | 🟢 Intermediate | 🔵 Advanced | 🔴 Expert || What  | What is the role of the CPU in a microcontroller?<br> What are registers and ALU?<br> What is CPU? | What is instruction cycle? | What is pipeline hazard? | What is worst-case execution time (WCET)? |
| Why   | Why is the CPU considered the “brain” of the microcontroller?<br> Why is CPU needed? | Why use registers instead of memory? | Why do interrupts require context saving? | Why is WCET critical in real-time systems? |
| How   | How does the CPU fetch, decode, and execute instructions?<br> How does it execute instructions? | How does ALU perform operations? | How does stack corruption occur? | How do you guarantee deterministic execution? |
| Where | Where are registers located and how are they accessed?<br> Where are registers used? | Where are flags used? | Where does CPU waste cycles? | Where do hidden CPU stalls occur? |
| When  | When does the CPU use interrupts vs polling?<br> When does CPU stop executing? | When does branching occur? | When does instruction reordering cause bugs? | When does speculative execution fail? |
| Who   | Who controls CPU operation (clock, instruction set architecture)?<br> Who controls instruction flow? | Who manages stack operations? | Who optimizes assembly vs compiler? | Who validates timing guarantees? |

-----

# 3. Memory (RAM, ROM, Flash, EEPROM)

| Type  | Questions                                                                                              |      |       |      |
| ----- | ------------------------------------------------------------------------------------------------------ | ---- | ----- | ---- |
|       | 🟡 Basic | 🟢 Intermediate | 🔵 Advanced | 🔴 Expert |
| What  | What are RAM, ROM, Flash, and EEPROM?<br> What is the difference between volatile and non-volatile memory? | What is stack vs heap? | What is fragmentation? | What is memory protection in MCUs? |
| Why   | Why do microcontrollers use different types of memory?<br> Why do we need memory? | Why avoid dynamic allocation? | Why is alignment important? | Why is ECC important? |
| How   | How is data stored and retrieved from memory?<br> How is data stored? | How does memory addressing work? | How does caching affect memory access? | How do you design memory-safe firmware? |
| Where | Where is program code stored? Where is temporary data stored?<br> Where is program stored? | Where do variables reside? | Where do memory leaks occur? | Where do silent data corruptions happen? | 
| When  | When is EEPROM preferred over Flash? When is RAM cleared? | When does stack overflow occur? | When does Flash degrade? | When should wear leveling be implemented? |
| Who   | Who manages memory allocation (compiler, programmer)?<br> Who allocates memory? | Who controls linker memory mapping? | Who manages persistent data storage? | Who audits memory reliability? |

-----

# 4. GPIO (General Purpose Input/Output)

| Type  | Questions                                                 | | | |
| ----- | --------------------------------------------------------- | ----- | ----- | ----- |
| What  | What is GPIO?<br> What are input and output pins?         | What is pull-up/down resistor? | What is open-drain configuration? | What is signal integrity issue? |
| Why   | Why are GPIO pins essential in microcontrollers?          | Why do inputs float? | Why is current limiting required? | Why do high-speed GPIO fail? |
| How   | How do you configure a GPIO pin as input or output?       | How to read digital input? | How does EMI affect GPIO? | How to design robust I/O interfaces? |
| Where | Where are GPIO pins used in real systems (LEDs, buttons)? | Where do switching errors occur? | Where do short circuits occur? | Where do metastability issues occur? |
| When  | When should pull-up or pull-down resistors be used?       | When to debounce signals? | When does GPIO latch-up happen? | When does synchronization fail? |
| Who   | Who controls GPIO states (software vs hardware)?          | Who designs interface circuits? | Who ensures hardware protection? | Who validates hardware reliability? |

-----

# 5. Clock System

| Type  | Questions                                                                   |
| ----- | --------------------------------------------------------------------------- |
| What  | What is a clock in a microcontroller? What are internal vs external clocks? |
| Why   | Why is a clock necessary for operation?                                     |
| How   | How does clock frequency affect performance and power consumption?          |
| Where | Where is the clock signal distributed inside the MCU?                       |
| When  | When should you use an external crystal oscillator?                         |
| Who   | Who configures the clock system (developer, firmware)?                      |

-----

6. Timers and Counters

| Type  | Questions                                                              |
| ----- | ---------------------------------------------------------------------- |
| What  | What are timers and counters? What is the difference between them?     |
| Why   | Why are timers used in embedded systems?                               |
| How   | How do timers generate delays, PWM signals, or measure time intervals? |
| Where | Where are timers used (motor control, signal measurement)?             |
| When  | When should interrupts be used with timers?                            |
| Who   | Who configures timer modes and prescalers?                             |

-----

7. Interrupts

| Type  | Questions                                                        | | | |
| ----- | ---------------------------------------------------------------- | --- | --- | --- |
| What  | What is an interrupt? What are ISR (Interrupt Service Routines)? | What is interrupt priority? |  What is race condition? | What is interrupt jitter? |
| Why   | Why are interrupts important in real-time systems?<br> Why use interrupts? | Why avoid long ISR? | Why need critical sections? | Why does ISR timing vary? |
| How   | How does interrupt handling work step-by-step?<br> How does ISR work? | How does nesting work? | How does reentrancy issue occur? | How to design lock-free systems? |
| Where | Where are interrupts used (sensors, communication)? | Where does latency occur? | Where does data corruption happen? | Where do deadlocks occur? |
| When  | When should interrupts be avoided?<br> When triggered? | When disable interrupts? | When use mutex/flags? | When does priority inversion happen? |
| Who   | Who prioritizes interrupts (hardware controller)?<br> Who handles ISR? | Who schedules execution? | Who ensures concurrency safety? | Who validates real-time safety? |

-----

8. Communication Protocols (UART, SPI, I2C)

| Type  | Questions                                                   |
| ----- | ----------------------------------------------------------- |
| What  | What are UART, SPI, and I2C protocols?                      |
| Why   | Why do we need communication protocols in microcontrollers? |
| How   | How does data transfer happen in each protocol?             |
| Where | Where are these protocols used (sensors, displays)?         |
| When  | When should you choose SPI over I2C or UART?                |
| Who   | Who initiates communication (master/slave roles)?           |

-----

9. ADC (Analog to Digital Converter)

| Type  | Questions                                                     |
| ----- | ------------------------------------------------------------- |
| What  | What is ADC? What is resolution and sampling rate?            |
| Why   | Why is ADC required in embedded systems?                      |
| How   | How does ADC convert analog signals to digital values?        |
| Where | Where is ADC used (temperature sensors, voltage measurement)? |
| When  | When should you use higher resolution ADC?                    |
| Who   | Who configures ADC channels and reference voltage?            |

-----

10. PWM (Pulse Width Modulation)

| Type  | Questions                                             |
| ----- | ----------------------------------------------------- |
| What  | What is PWM? What is duty cycle?                      |
| Why   | Why is PWM used instead of analog output?             |
| How   | How is PWM generated using timers?                    |
| Where | Where is PWM used (motor speed control, LED dimming)? |
| When  | When should PWM frequency be adjusted?                |
| Who   | Who controls PWM parameters (firmware)?               |

-----

11. Power Management

| Type  | Questions                                             |
| ----- | ----------------------------------------------------- |
| What  | What are power modes (sleep, idle)?                   |
| Why   | Why is power management critical in embedded systems? |
| How   | How does a microcontroller reduce power consumption?  |
| Where | Where are low-power modes used (battery devices)?     |
| When  | When should sleep mode be activated?                  |
| Who   | Who decides power optimization strategy?              |

-----

12. Embedded Programming

| Type  | Questions                                                      |
| ----- | -------------------------------------------------------------- |
| What  | What programming languages are used (C, Embedded C, Assembly)? |
| Why   | Why is C widely used in microcontroller programming?           |
| How   | How do you compile and upload code to a microcontroller?       |
| Where | Where is embedded software stored and executed?                |
| When  | When should low-level programming be used?                     |
| Who   | Who develops embedded firmware?                                |

-----

13. Debugging and Development Tools

| Type  | Questions                                         |
| ----- | ------------------------------------------------- |
| What  | What are debuggers, simulators, and IDEs?         |
| Why   | Why is debugging important in embedded systems?   |
| How   | How do you troubleshoot microcontroller programs? |
| Where | Where are debugging tools connected (JTAG, SWD)?  |
| When  | When should hardware debugging be used?           |
| Who   | Who uses debugging tools (engineers, developers)? |

-----

| Type  | Questions                                                                                  |
| ----- | ------------------------------------------------------------------------------------------ |
| What  | What happens if an interrupt modifies a variable used in the main loop without protection? |
| Why   | Why is real-time behavior hard to guarantee in complex systems?                            |
| How   | How do you design a fail-safe system using watchdog timers?                                |
| Where | Where do hardware-software integration issues typically occur?                             |
| When  | When does adding more features reduce system reliability?                                  |
| Who   | Who is responsible for system-level debugging—hardware or software engineer?               |
