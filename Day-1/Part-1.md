# CMOS Circuit Design & SPICE Simulation Workshop

> A comprehensive guide to understanding CMOS transistor behavior, circuit analysis, and practical SPICE simulation using Sky130 technology.

[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/yourusername/cmos-spice-workshop)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Stars](https://img.shields.io/github/stars/yourusername/cmos-spice-workshop?style=social)](https://github.com/yourusername/cmos-spice-workshop/stargazers)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Module 1: Circuit Design Fundamentals](#module-1-circuit-design-fundamentals)
- [Module 2: NMOS Transistor Physics](#module-2-nmos-transistor-physics)
- [Module 3: Hands-on SPICE Simulation](#module-3-hands-on-spice-simulation)
- [Getting Started](#getting-started)
- [Repository Structure](#repository-structure)
- [Resources](#resources)
- [Contributing](#contributing)

---

## Overview

This workshop provides an in-depth exploration of CMOS circuit design through theoretical concepts and practical SPICE simulations. You'll learn to model, simulate, and analyze transistor-level circuits using industry-standard tools and the open-source Sky130 PDK.

**What You'll Learn:**
- Transistor operation principles and I-V characteristics
- Circuit simulation methodology using SPICE
- Timing analysis and delay characterization
- Real-world circuit design using Sky130 technology models

---

## Module 1: Circuit Design Fundamentals

### The Role of SPICE in Modern Circuit Design

SPICE (Simulation Program with Integrated Circuit Emphasis) serves as the backbone of pre-silicon verification, enabling designers to validate circuit behavior without costly fabrication runs.

#### Key Capabilities

| Purpose | Benefit |
|---------|---------|
| **Functional Verification** | Validate logic correctness before tape-out |
| **Performance Analysis** | Measure propagation delays and signal integrity |
| **Power Estimation** | Calculate static and dynamic power consumption |
| **Design Optimization** | Iterate rapidly on transistor sizing and topology |

#### Practical Example: CMOS Inverter Analysis

The CMOS inverter represents the fundamental building block of digital circuits. Its operation demonstrates complementary transistor behavior:

<p align="center">
  <img src="assets/images/cmos_inverter_schematic.png" alt="CMOS Inverter Circuit" width="600"/>
  <br>
  <em>Figure 1: CMOS Inverter Circuit Diagram</em>
</p>

**Circuit Topology:**
- PMOS transistor connected between VDD and output node
- NMOS transistor connected between output node and ground
- Both gates tied to the input signal
- Load capacitance (CL) models downstream circuit loading

**Switching Behavior:**

| Input State | PMOS | NMOS | Output | Current Path |
|-------------|------|------|--------|--------------|
| LOW (0V) | Conducting | Cut-off | HIGH (VDD) | VDD → PMOS → Output |
| HIGH (VDD) | Cut-off | Conducting | LOW (0V) | Output → NMOS → GND |

#### Simulation Outputs

SPICE generates two critical characteristics:

<p align="center">
  <img src="assets/images/inverter_iv_vtc_curves.png" alt="Inverter Characteristics" width="700"/>
  <br>
  <em>Figure 2: CMOS Inverter I-V and VTC Characteristics</em>
</p>

1. **Current-Voltage (I-V) Curves**: Shows drain current vs. output voltage across different input conditions
2. **Voltage Transfer Characteristic (VTC)**: Maps input voltage to output voltage, revealing noise margins and switching threshold

---

### Understanding Timing Characterization

Cell delays are not constant—they depend on environmental factors that must be accurately modeled.

#### Delay Lookup Tables (LUTs)

Modern timing libraries use 2D lookup tables indexed by:
- **Input Slew**: Transition time of the input signal edge
- **Output Load**: Capacitive loading at the output pin

<p align="center">
  <img src="assets/images/delay_lut_structure.png" alt="Delay LUT Structure" width="650"/>
  <br>
  <em>Figure 3: Delay Lookup Table Organization</em>
</p>

**Example LUT Structure:**

```
Input Slew ↓ | 10fF  | 30fF  | 50fF  | 70fF  | 90fF  | 110fF |
-------------|-------|-------|-------|-------|-------|-------|
20ps         | x1    | x2    | x3    | x4    | x5    | x6    |
40ps         | x7    | x8    | x9    | x10   | x11   | x12   |
60ps         | x13   | x14   | x15   | x16   | x17   | x18   |
80ps         | x19   | x20   | x21   | x22   | x23   | x24   |
```

#### Calculating Total Output Capacitance

When a gate drives multiple fanouts, compute total capacitance as:

**C_total = C_pin_output + Σ(C_pin_input_fanouts) + Σ(C_wire_segments)**

<p align="center">
  <img src="assets/images/capacitance_calculation_example.png" alt="Capacitance Calculation" width="600"/>
  <br>
  <em>Figure 4: Total Capacitance Calculation for Multi-Fanout Scenario</em>
</p>

**Practical Scenario:**

For gate G1 driving gates G2, G3, and G4 through an interconnect network:

```
C_total(G1) = C_out(G1) + C_in(G2) + C_in(G3) + C_in(G4) 
              + C_wire1 + C_wire2 + C_wire3 + C_wire4
```

#### Delay Interpolation Technique

When required conditions fall between LUT entries, use linear interpolation:

<p align="center">
  <img src="assets/images/delay_interpolation_method.png" alt="Delay Interpolation" width="550"/>
  <br>
  <em>Figure 5: Linear Interpolation for Delay Calculation</em>
</p>

**Given:** Input slew = 40ps, Output load = 60fF  
**LUT contains:** Delays at 50fF (x9) and 70fF (x10)

**Interpolation Formula:**

```
Delay@60fF = x9 + [(60fF - 50fF) / (70fF - 50fF)] × (x10 - x9)
           = x9 + [10fF / 20fF] × (x10 - x9)
           = x9 + 0.5 × (x10 - x9)
```

⚠️ **Important**: Extrapolation beyond table boundaries yields less accurate results. Design within characterized ranges whenever possible.

---

## Module 2: NMOS Transistor Physics

### Transistor Structure and Operation

An NMOS transistor consists of four terminals:
- **Gate (G)**: Control terminal
- **Drain (D)**: Current output
- **Source (S)**: Current input
- **Bulk/Body (B)**: Substrate connection

<p align="center">
  <img src="assets/images/nmos_structure_diagram.png" alt="NMOS Structure" width="500"/>
  <br>
  <em>Figure 6: NMOS Transistor Physical Structure</em>
</p>

#### Operating Regions

<p align="center">
  <img src="assets/images/nmos_operating_regions.png" alt="NMOS Operating Regions" width="700"/>
  <br>
  <em>Figure 7: NMOS Transistor Operating Regions</em>
</p>

**Cut-off (V_GS < V_th):**
- No inversion channel exists
- Drain current ≈ 0 (only leakage)
- Transistor acts as open switch

**Triode/Linear (V_GS > V_th, V_DS < V_GS - V_th):**
- Continuous channel from source to drain
- Current proportional to (V_GS - V_th)·V_DS
- Transistor behaves as voltage-controlled resistor

**Saturation (V_GS > V_th, V_DS ≥ V_GS - V_th):**
- Channel pinched off near drain
- Current relatively independent of V_DS
- Current proportional to (V_GS - V_th)²

### Body Effect (Substrate Bias)

When source and body voltages differ (V_SB > 0), the threshold voltage increases:

<p align="center">
  <img src="assets/images/body_effect_comparison.png" alt="Body Effect" width="650"/>
  <br>
  <em>Figure 8: Body Effect on Threshold Voltage</em>
</p>

**Modified Threshold Voltage:**

<p align="center">
  <img src="assets/images/body_effect_equation.png" alt="Body Effect Equation" width="400"/>
</p>

```
V_th(V_SB) = V_th0 + γ × [√(|2φ_F| + V_SB) - √(|2φ_F|)]
```

Where:
- **γ**: Body effect coefficient (provided by foundry)
- **φ_F**: Fermi potential (material constant)
- **V_th0**: Zero-bias threshold voltage

**Physical Explanation:**
- Reverse bias widens depletion region
- Requires stronger gate voltage to achieve inversion
- Affects stacked transistors in logic gates

### Current Equations

#### Linear Region Model

<p align="center">
  <img src="assets/images/nmos_linear_region.png" alt="Linear Region Operation" width="600"/>
  <br>
  <em>Figure 9: NMOS Operating in Linear Region</em>
</p>

For V_DS < V_GS - V_th:

```
I_D = μ_n × C_ox × (W/L) × [(V_GS - V_th) × V_DS - V_DS²/2]
```

#### Saturation Region Model

<p align="center">
  <img src="assets/images/nmos_saturation_region.png" alt="Saturation Region Operation" width="600"/>
  <br>
  <em>Figure 10: NMOS Operating in Saturation Region (Pinch-off)</em>
</p>

For V_DS ≥ V_GS - V_th:

```
I_D = (μ_n × C_ox × W) / (2L) × (V_GS - V_th)² × (1 + λ × V_DS)
```

Where:
- **μ_n**: Electron mobility
- **C_ox**: Gate oxide capacitance per unit area
- **W/L**: Width-to-length ratio
- **λ**: Channel length modulation parameter

### Drift Current Mechanism

Current flow results from electric field-induced carrier drift:

<p align="center">
  <img src="assets/images/drift_current_illustration.png" alt="Drift Current" width="550"/>
  <br>
  <em>Figure 11: Drift Current Mechanism in NMOS Channel</em>
</p>

**Drift Velocity:**
```
v_drift = μ_n × E_field
```

**Total Drain Current:**
```
I_D = (Charge density) × (Drift velocity) × (Channel width)
I_D = Q_i(x) × v(x) × W
```

This forms the foundation for deriving transistor current equations from first principles.

---

## Module 3: Hands-on SPICE Simulation

### Simulation Hierarchy

VLSI design tools operate at multiple abstraction levels:

<p align="center">
  <img src="assets/images/simulation_hierarchy.png" alt="Simulation Hierarchy" width="650"/>
  <br>
  <em>Figure 12: VLSI Design Simulation Hierarchy</em>
</p>

| Level | Tool Examples | Purpose |
|-------|---------------|---------|
| **Process** | SUPREME, TCAD | Model fabrication effects on devices |
| **Circuit** | SPICE, Spectre, HSPICE | Analyze transistor-level behavior |
| **Logic** | VCS, ModelSim, Questa | Verify HDL functionality |
| **Architecture** | Gem5, SystemC | Evaluate system performance |

### SPICE Analysis Types

| Analysis | Command | Use Case |
|----------|---------|----------|
| **DC Operating Point** | `.op` | Find steady-state voltages/currents |
| **DC Sweep** | `.dc` | Generate I-V characteristics |
| **Transient** | `.tran` | Time-domain waveform analysis |
| **AC** | `.ac` | Frequency response analysis |
| **Noise** | `.noise` | Device noise characterization |

### SPICE Netlist Syntax

#### Component Declaration

**MOSFET:**
```spice
M<n> <drain> <gate> <source> <bulk> <model> W=<width> L=<length>
```

**Resistor:**
```spice
R<n> <node1> <node2> <resistance>
```

**Voltage Source:**
```spice
V<n> <positive_node> <negative_node> <DC_value>
```

#### Example: NMOS Characterization Circuit

<p align="center">
  <img src="assets/images/nmos_test_circuit.png" alt="NMOS Test Circuit" width="500"/>
  <br>
  <em>Figure 13: NMOS Characterization Test Circuit</em>
</p>

```spice
*** NMOS I-V Characteristic Measurement ***

*** Technology File ***
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*** Circuit Netlist ***
M1 vdd n1 0 0 sky130_fd_pr__nfet_01v8 W=1.8u L=1.2u
R1 in n1 55
Vdd vdd 0 2.5
Vin in 0 2.5

*** Simulation Control ***
.dc Vdd 0 2.5 0.01 Vin 0 2.5 0.5

.control
run
plot -i(Vdd)
.endc

.end
```

**Netlist Breakdown:**

| Line | Component | Description |
|------|-----------|-------------|
| `M1 vdd n1 0 0 ...` | NMOS transistor | Drain=vdd, Gate=n1, Source=GND, Bulk=GND |
| `R1 in n1 55` | Gate resistor | 55Ω between input and gate (limits current) |
| `Vdd vdd 0 2.5` | Supply voltage | 2.5V DC source |
| `Vin in 0 2.5` | Gate voltage | Sweeps gate bias |
| `.dc Vdd 0 2.5 0.01` | DC sweep | V_DS sweep with 10mV steps |

---

## Getting Started

### Prerequisites

- Basic understanding of semiconductor physics
- Familiarity with circuit theory
- Linux-based operating system (recommended)

### Installation

**Clone the workshop repository:**

```bash
git clone https://github.com/yourusername/cmos-spice-workshop.git
cd cmos-spice-workshop
```

**Install ngspice (open-source SPICE simulator):**

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install ngspice

# Fedora/RHEL
sudo dnf install ngspice

# macOS
brew install ngspice
```

### Running Your First Simulation

**Execute a SPICE netlist:**

```bash
cd simulations
ngspice nmos_iv_characteristics.sp
```

**Generate plots:**

```spice
ngspice 1 -> plot -vdd#branch
ngspice 2 -> plot i(M1)
```

**Save waveform data:**

```spice
ngspice 3 -> write simulation_output.raw
ngspice 4 -> quit
```

### Example Simulation: NMOS I-V Curves

**Netlist (nmos_iv_characteristics.sp):**

```spice
*** NMOS DC Characteristic - Sky130 ***

.param temp=27

.lib "models/sky130_fd_pr/sky130.lib.spice" tt

*** Device Under Test ***
XM1 vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55

*** Voltage Sources ***
Vdd vdd 0 1.8
Vin in 0 1.8

*** Sweep V_DS for multiple V_GS values ***
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control
run
display
setplot dc1
plot -vdd#branch
.endc

.end
```

**Expected Output:**

<p align="center">
  <img src="assets/images/nmos_iv_simulation_result.png" alt="NMOS I-V Curves" width="700"/>
  <br>
  <em>Figure 14: Simulated NMOS I-V Characteristics (W=5µm, L=2µm, Sky130)</em>
</p>

- Family of I_D vs V_DS curves
- Each curve represents different V_GS value
- Clearly shows linear and saturation regions

---

## Repository Structure

```
cmos-spice-workshop/
├── README.md                          # This file
├── LICENSE                            # License information
├── .gitignore                         # Git ignore rules
│
├── assets/                            # 📁 All images and media files
│   └── images/
│       ├── cmos_inverter_schematic.png
│       ├── inverter_iv_vtc_curves.png
│       ├── delay_lut_structure.png
│       ├── capacitance_calculation_example.png
│       ├── delay_interpolation_method.png
│       ├── nmos_structure_diagram.png
│       ├── nmos_operating_regions.png
│       ├── body_effect_comparison.png
│       ├── body_effect_equation.png
│       ├── nmos_linear_region.png
│       ├── nmos_saturation_region.png
│       ├── drift_current_illustration.png
│       ├── simulation_hierarchy.png
│       ├── nmos_test_circuit.png
│       └── nmos_iv_simulation_result.png
│
├── simulations/                       # 📁 SPICE netlists
│   ├── day1_nmos_iv_characteristics.sp
│   ├── day2_nmos_transfer_char.sp
│   ├── day3_inverter_vtc.sp
│   ├── day4_inverter_noise_margin.sp
│   ├── day5_inverter_supply_variation.sp
│   └── examples/
│       ├── basic_inverter.sp
│       ├── nand_gate.sp
│       └── ring_oscillator.sp
│
├── models/                            # 📁 Technology files
│   └── sky130_fd_pr/
│       ├── models/
│       │   └── sky130.lib.spice      # Main model library
│       └── cells/
│           ├── nfet_01v8/            # NMOS models
│           └── pfet_01v8/            # PMOS models
│
├── results/                           # 📁 Simulation outputs
│   ├── waveforms/
│   └── plots/
│
├── scripts/                           # 📁 Helper scripts
│   ├── run_all_simulations.sh
│   ├── plot_results.py
│   └── compare_corners.sh
│
└── docs/                              # 📁 Additional documentation
    ├── setup_guide.md
    ├── troubleshooting.md
    └── advanced_topics.md
```

### 📸 Image Guidelines

**To add images to your repository:**

1. **Create the assets directory:**
   ```bash
   mkdir -p assets/images
   ```

2. **Add your images:**
   ```bash
   cp your_image.png assets/images/
   ```

3. **Reference in Markdown:**
   ```markdown
   ![Alt Text](assets/images/your_image.png)
   
   # Or with centered alignment and caption:
   <p align="center">
     <img src="assets/images/your_image.png" alt="Description" width="600"/>
     <br>
     <em>Figure X: Your caption here</em>
   </p>
   ```

4. **Image naming convention:**
   - Use lowercase with underscores: `nmos_iv_curves.png`
   - Be descriptive: `inverter_vtc_analysis.png`
   - Include version if needed: `layout_v2.png`

5. **Recommended image formats:**
   - `.png` for diagrams and screenshots
   - `.jpg` for photographs
   - `.svg` for vector graphics (scalable)

---

## Resources

### Technology Files

**Sky130 Model Locations:**

1. **NMOS Model (Typical Corner):**
   ```
   models/sky130_fd_pr/cells/nfet_01v8/sky130_fd_pr__nfet_01v8__tt.pm3.spice
   ```

2. **Process Corner Models:**
   ```
   models/sky130_fd_pr/cells/nfet_01v8/sky130_fd_pr__nfet_01v8__tt.corner.spice
   ```

3. **Complete Model Library:**
   ```
   models/sky130_fd_pr/models/sky130.lib.spice
   ```

### Key Parameters in Sky130 Models

| Parameter | Symbol | Typical Value | Description |
|-----------|--------|---------------|-------------|
| Threshold Voltage | V_th | ~0.45V | Gate voltage for strong inversion |
| Oxide Capacitance | C_ox | ~6.9 fF/µm² | Gate oxide cap per unit area |
| Electron Mobility | μ_n | ~400 cm²/V·s | Carrier mobility |
| Body Effect Coeff. | γ | ~0.45 V^0.5 | Substrate bias sensitivity |

### Further Learning

- **SPICE Documentation**: [Ngspice Manual](http://ngspice.sourceforge.net/docs/ngspice-manual.pdf)
- **Sky130 PDK**: [SkyWater PDK Documentation](https://skywater-pdk.readthedocs.io/)
- **VLSI Design**: *CMOS VLSI Design* by Weste and Harris
- **Original Workshop**: [VSD Hardware Design Program](https://github.com/kunalg123/sky130CircuitDesignWorkshop)

---

## Contributing

We welcome contributions! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes and commit:**
   ```bash
   git commit -am "Add new simulation example"
   ```
4. **Push to your fork:**
   ```bash
   git push origin feature/your-feature-name
   ```
5. **Submit a Pull Request**

### Contribution Guidelines

- Follow the existing code style and formatting
- Add documentation for new simulations
- Include example outputs/waveforms
- Update the README if adding new features
- Test your SPICE netlists before submitting

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- **VSD Corp** for the original workshop content
- **SkyWater Technology** for the open-source PDK
- **UC Berkeley** for developing SPICE
- All contributors to this repository

---

## Contact

- **Issues**: [GitHub Issues](https://github.com/yourusername/cmos-spice-workshop/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/cmos-spice-workshop/discussions)

---

<p align="center">
  Made with ❤️ for the VLSI community
  <br>
  <strong>Happy Simulating! 🚀</strong>
</p>