# RISC-V-Tapeout-Week-2

# Fundamentals of System-on-Chip (SoC) Design and the Role of VSDBabySoC

## What is a System-on-Chip (SoC)?

A System-on-Chip (SoC) is an integrated circuit that combines all essential components of a computer—or any other electronic system—onto a single chip. It replaces the need for multiple discrete chips by including the central processing unit (CPU), memory, input/output (I/O), and sometimes additional features, efficiently packaged in a way that saves space and power. SoCs are crucial for compact electronics like smartphones, wearables, IoT devices, and embedded systems, where minimizing power consumption and maximizing performance are critical[attached_file:1].

## Components of a Typical SoC

A well-designed SoC brings together several subsystems, typically including:

- **CPU (Central Processing Unit)**: The main processing core, executing program instructions and handling computational tasks[attached_file:1].
- **Memory**: Volatile (RAM) for temporary data storage and non-volatile (ROM/Flash) for program and persistent storage[attached_file:1].
- **I/O Ports**: Interfaces for communicating with external peripherals and subsystems, such as cameras, displays, USB, and sensors[attached_file:1].
- **Interconnect**: Buses or crossbars connecting the CPU, memory, and peripherals, ensuring fast data transfer within the chip.
- **Graphics Processing Unit (GPU)**: Dedicated hardware for graphics and display tasks, if required[attached_file:1].
- **Digital Signal Processor (DSP)**: Handles specialized tasks like audio and video signal processing when complex multimedia work is needed[attached_file:1].
- **Power Management Unit**: Optimizes energy usage across the chip to extend battery life in portable devices[attached_file:1].
- **Special Features**: Such as wireless radios (Wi-Fi, Bluetooth), hardware security modules, and analog components like DACs/ADCs, depending on the SoC's intended use[attached_file:1].

## Why VSDBabySoC is a Simplified Model for Learning

**VSDBabySoC** is designed as an educational, compact SoC model based on the open-source RISC-V architecture, serving as an approachable project for grasping modern SoC design concepts. Its structure allows beginners to learn the integration workflow without the overwhelming complexity of industrial-scale chips[attached_file:1].

VSDBabySoC highlights include:
- An RVMYTH microprocessor (RISC-V based), modeling the CPU core in mainstream SoCs.
- A Phase-Locked Loop (PLL) for real SoC clock generation and signal synchronization.
- A 10-bit Digital-to-Analog Converter (DAC) for interfacing with external analog electronics (e.g., TVs, phones), demonstrating digital-analog interaction.
- Documented design flow and modular integration, enabling hands-on learning about SoC architecture and mixed-signal interfacing[attached_file:1].

By mimicking the core architecture and essential subsystems of real SoCs in a minimalistic, transparent manner, VSDBabySoC provides a stepping stone from academic theory to practical understanding, before tackling advanced topics like operating system porting and high-performance customized designs[attached_file:1].

## The Role of Functional Modelling Before RTL and Physical Design

Functional modelling is a critical preparatory step in the SoC design flow. In this phase, the system's behavior and integration are modeled and simulated at a high level, focusing on verifying that the chip will meet functional requirements before investing in the more laborious and expensive stages:
- **RTL (Register Transfer Level) Design**: After functional models are validated, the next step is to precisely describe module behavior and connectivity using HDLs (e.g., Verilog, SystemVerilog).
- **Physical Design**: RTL code is mapped to silicon through synthesis, placement, routing, and further implementation steps.

Key benefits of functional modelling include:
- Detecting logic errors early in the workflow.
- Allowing for rapid prototyping and experimentation with system architectures.
- Facilitating discussions between hardware, software, and system engineers by providing an executable specification[attached_file:1].

For VSDBabySoC, functional modelling validates the correct integration of the RVMYTH processor, PLL, and DAC before hardware-level design is pursued, reinforcing the importance of thorough testing and correct architectural choices in real-world SoC development[attached_file:1].

---

This approach ensures strong conceptual foundations, making VSDBabySoC an ideal platform for students and engineers beginning their SoC design journey.


---

# Introduction to the VSDBabySoC

The VSDBabySoC is a compact yet highly capable SoC based on the RISC-V architecture. Its primary objective is to serve as a test platform that brings together three open-source IP cores for the first time while enabling calibration of its analog components. VSDBabySoC integrates an RVMYTH microprocessor, an 8x phase-locked loop (PLL) for stable clock generation, and a 10-bit digital-to-analog converter (DAC) for interaction with analog devices.

![vsdbabysoc_block_diagram](images/vsdbabysoc_block_diagram.png)

## Problem Statement

This project explores different facets involved in designing a minimal SoC centered around the RVMYTH processor (a RISC-V core). The SoC utilizes a PLL for its clock subsystem and a 10-bit DAC for external I/O. Analog-aware devices such as TVs or phones can process DAC outputs to offer users audio or video content. By leveraging open-source IPs and Sky130 fabrication, the VSDBabySoC is suitable for academic and educational applications.


## What is RVMYTH?

RVMYTH is a straightforward RISC-V-based CPU, created as part of a collaborative workshop led by RedwoodEDA and VSD. Over five days, participants—including students at the middle-school level—built a working processor from the ground up using TL-Verilog (TLV) for rapid iteration. The design and all future enhancements are licensed openly, contributed by students.

## What is a PLL?

A phase-locked loop (PLL) is a feedback-controlled system that generates an output signal whose phase follows that of an input reference. PLLs see widespread use for phase synchronization, frequency synthesis, and clock distribution.

## What is a DAC?

A digital-to-analog converter (DAC) transforms digital data into corresponding analog signals. DACs are integral to communications, enabling generation of analog output from digital systems. High-speed DACs support mobile networks and optical communications.

# Modeling the VSDBabySoC

This section details the simulation workflow, using `iverilog` for digital modeling and `gtkwave` for waveform visualization. Initial input stimuli trigger the PLL within the `vsdbabysoc` module, which begins clocking the `rvmyth` core. The processor executes programs stored in instruction memory, filling register `r17` with sample values each cycle. These values feed the DAC to generate the final analog output (`OUT`). The SoC wraps three main IP cores, with an accompanying testbench for evaluation.

> **Note:** Referenced repositories for modeling are listed below for completeness; the main source files reside in the [Source-Code Directory](src), with module implementations in the [Modules Sub-Directory](src/module).

## RVMYTH Modeling

As explained above, RVMYTH is implemented in TL-Verilog; to leverage it in our Verilog-based SoC, we must first transpile it with a tool like `sandpiper-saas`.

Reference: [RVMYTH core modeling repo](https://github.com/shivanishah269/risc-v-core)

## PLL and DAC Modeling

Native analog synthesis in Verilog remains unsupported; however, simulation is possible with the `real` data type. The following repositories provided the design basis for the PLL and DAC:

1. [PLL modeling reference](https://github.com/vsdip/rvmyth_avsdpll_interface)
2. [DAC modeling reference](https://github.com/vsdip/rvmyth_avsddac_interface)

> **Caution:** Early in development, the Verilog PLL model was sourced from [here](https://github.com/vsdip/rvmyth_avsdpll_interface). As the design advanced to physical implementation, some changes were made to yield a new variant (`AVSDPLL`), based on [this IP](https://github.com/lakshmi-sathi/avsdpll_1v8).

## Step-by-Step Modeling Guide

This section walks through the complete modeling process, adjusting digital output stimuli to observe their effect at the DAC. Commands are tested on Ubuntu Bionic (18.04.5); other systems may need adjustments.

1. Install required packages:

    ```
    sudo apt install make python python3 python3-pip git iverilog gtkwave docker.io
    sudo chmod 666 /var/run/docker.sock
    cd ~
    pip3 install pyyaml click sandpiper-saas
    ```

2. Clone the project repository (example uses home directory):

    ```
    cd ~
    git clone https://github.com/manili/VSDBabySoC.git
    ```

3. Generate the pre-synthesis simulation file:

    ```
    cd VSDBabySoC
    make pre_synth_sim
    ```
    The simulation output (`pre_synth_sim.vcd`) is saved to `output/pre_synth_sim`.

4. Visualize the signal waveforms:

    ```
    gtkwave output/pre_synth_sim/pre_synth_sim.vcd
    ```
    Focus on the `CLK` (provided by the PLL) and `OUT` (output of the DAC model). An example result of the process:

    <img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/34aa7fb2-6862-4e60-8af7-5ef838b2814b" />

Key signals seen here include:

- **CLK:** The input clock to the RVMYTH core, originating from the PLL.
- **reset:** The RVMYTH core’s reset input, typically driven externally.
- **OUT:** The VSDBabySoC’s output, produced by the DAC (modeled digitally due to simulation limitations).
- **RV_TO_DAC[9:0]:** The 10-bit output bus from RVMYTH register #17.
- **OUT (real):** An analog-typed output wire from the DAC, representing actual analog behavior in simulation.

> **Note:** The synthesis flow cannot support `real` type signals. Thus, the `vsdbabysoc.OUT` must be a regular `wire`, which tools like `iverilog` treat as digital only. Therefore, analog output is best observed using the `dac.OUT` (of type `real`) within simulation results.






