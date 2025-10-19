# 🌍 RISC-V System-on-Chip Development Initiative - VSD
## 🖥️ Understanding Velocity Saturation Effects and CMOS Inverter Transfer Characteristics

### 🌶️ <ins>Advanced Node SPICE Analysis and Carrier Velocity Saturation: </ins>

When analyzing modern semiconductor devices at smaller technology nodes, velocity saturation becomes a critical phenomenon affecting transistor performance.

#### **<ins>Operating in the Linear Domain:</ins>**
- The transistor functions as a resistor controlled by gate voltage
- This mode is active when:
  $$V_{DS} < V_{GS} - V_T$$
- The drain current follows:

    $$I_D = k_n \left[ (V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2} \right] $$

#### **<ins>Saturation Mode Operation:</ins>**
- Device acts as a controlled current source
- Activated under the condition:
  $$V_{DS} \geq V_{GS} - V_T$$
- Current behavior is described by:
<p align="center">
    $$I_D = \frac{k_n}{2} \frac{W}{L} (V_{GS} - V_T)^2 [1 + \lambda V_{DS}]$$
</p>

### <ins>Square-Law Current Relationship:</ins>
As the gate-source voltage $V_{GS}$ exceeds the threshold $V_T$, an electron inversion layer develops beneath the gate oxide.

This relationship can be expressed as:

$$I_D \propto (V_{GS} - V_T)^2$$


### <ins>Carrier Velocity Saturation Phenomenon:</ins>
At elevated electric field intensities, carrier drift velocity reaches a maximum limit and ceases to scale linearly with field strength. This effect constrains the drain current below predictions from conventional square-law models.

The saturated current expression becomes:
<p align="center">
$$I_{Dsat} = W C_{ox} (V_{GS} - V_T) v_{sat}$$
</p>

### 📈 <ins>Voltage Transfer Characteristics of CMOS Inverters: </ins>

#### <ins>NMOS Transistor Switching Behavior: </ins>
When operating in the cutoff regime ($V_{GS} < V_T$), the NMOS device remains inactive—behaving like an open circuit with negligible current.

During linear/triode operation ($V_{GS} > V_T$ with small $V_{DS}$), the NMOS conducts actively—acting as a low-resistance path for current flow.

These characteristics make NMOS essential for both digital circuit design and analog switching applications.

#### <ins>Key Voltage-Current Parameters in MOS Devices:</ins>

- **$V_T$ (Threshold Voltage)**: Critical gate voltage required to establish the conducting inversion layer
- **$V_{GS}$ (Gate-to-Source Voltage)**: Primary control parameter for channel conductivity and current magnitude
- **$V_{DS}$ (Drain-to-Source Voltage)**: Defines whether the device operates in triode or saturation mode
- **$I_D$ (Drain Current)**: Output current determined by both $V_{GS}$ and $V_{DS}$ values

#### **<ins>Step 1 - Relating PMOS Gate Voltage to Input Signal</ins>**
For the PMOS device in a CMOS inverter:
- Source-gate voltage difference: $V_{SG} = V_{DD} - V_{in}$
- PMOS becomes active when $V_{SG} > |V_{TP}|$, which translates to $V_{DD} - V_{in} > |V_{TP}|$, or equivalently $V_{in} < V_{DD} - |V_{TP}|$
- PMOS enters cutoff when $V_{in} > V_{DD} - |V_{TP}|$

#### **<ins>Step 2 - Mapping PMOS Terminal Voltages to Output</ins>**
For the PMOS transistor configuration:
- Source-drain voltage: $V_{SD} = V_{DD} - V_{out}$
- Linear region operation requires: $V_{SD} < V_{SG} - |V_{TP}|$
- Saturation region begins when: $V_{SD} \geq V_{SG} - |V_{TP}|$

#### **<ins>Step 3 - NMOS Voltage Relationships with Output Node</ins>**
For the NMOS pull-down device:
- Terminal voltage difference: $V_{DS} = V_{out}$
- Triode operation occurs when: $V_{DS} < V_{GS} - V_{TN}$
- Saturation mode begins when: $V_{DS} \geq V_{GS} - V_{TN}$

#### **<ins>Step 4 - Constructing the Complete VTC Curve</ins>**

- **Generate characteristic curves** for both PMOS and NMOS devices
- **Identify operating points** where current equality holds: $I_{DN} = I_{DP}$  
- **Extract voltage pairs** $(V_{in}, V_{out})$ from these intersection points
- **Construct the transfer characteristic** illustrating all distinct operating regions

---

*This comprehensive analysis provides the foundation for understanding DC transfer characteristics in complementary MOS inverter circuits, essential for digital IC design.*
