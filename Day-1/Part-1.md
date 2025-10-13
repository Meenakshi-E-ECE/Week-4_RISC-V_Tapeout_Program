# Why SPICE Simulations?

### What is SPICE ?
SPICE (Simulation Program with Integrated Circuit Emphasis) is used to **analyze transistor-level circuits**. It helps designers understand **delay, noise, and variation effects** before fabrication.

### Need for SPICE Simulations
- Predict circuit **timing and delay** accurately.  
- Evaluate behavior under **different loads and input conditions**.  
- Analyze **transient, DC, and AC responses** of circuits.  

### Delay Table  
![delay_table](./Images/delay_table.png)
- Shows relationship between **input slew** and **output load**.  
- Helps in **estimating delay** based on input transition and output capacitance.  
>**Unit note:** 1 fF = 10⁻¹⁵ F (femtofarad)

### Source of Delay Table
- Typically generated from **SPICE simulations** by sweeping input slew and output capacitance.  
- Provides a **lookup table** used in **timing analysis / STA**.
