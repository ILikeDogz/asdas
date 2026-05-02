# Capacitor Design Tool User Documentation

## ECE 1259 AI-Assisted Engineering Design Project

### Spring 2026

---

## 1. Introduction

This document explains how to use our capacitor design tool and what the tool is computing in the background. The project was developed for the ECE 1259 AI-Assisted Engineering Design Project and focuses on capacitor analysis with a graphical interface, automated tests, and supporting documentation.

The project is organized into three main files:

- `capacitor.py`
  This is the backend. Created during phase 3 it handles the capacitor calculations, input validation, ESR-related functions, and the data structures returned to the GUI.

- `capacitor_gui.py`
  This is the user interface built with Tkinter and matplotlib, for phase 4.

- `test_capacitor.py`
  This file contains the required test cases described in phase 2 and some supplemental checks for the backend.

The finished tool lets a user choose a dielectric class and case size, enter a nominal capacitance and operating conditions, inspect the predicted outputs, and view both graphical and tabulated results.

---

## 2. Installation and Setup

### 2.1 Software Requirements

This project was developed in Python. The recommended setup is:

- Python 3.10 or newer
- `matplotlib`
- `pytest`
- standard-library `tkinter`

### 2.2 Required Project Files

To run the project, the main files needed are:

- `capacitor.py`
- `capacitor_gui.py`
- `test_capacitor.py`

### 2.3 Installing Dependencies

If the required Python packages not already available, install with:

```bash
python3 -m pip install matplotlib pytest
```

`tkinter` may need to be installed separately through the operating system package manager, though typically installed by default.

### 2.4 Running the GUI

```bash
python3 capacitor_gui.py
```

### 2.5 Running the Tests

```bash
python3 -m pytest test_capacitor.py -q
```

---

## 3. What the Tool Does

The tool helps a user design a capacitor and see how capacitor performance changes with dielectric type, geometry, capacitance, voltage, temperature, and frequency. The current workflow in the GUI uses:

- EIA dielectric class
- case size
- nominal capacitance
- capacitance unit
- operating voltage
- minimum and maximum frequency
- minimum and maximum temperature
- target ESR

From those inputs, the tool reports values such as:

- capacitance
- stored energy
- stored charge
- tan delta
- ESR range
- DC bias retention
- temperature validity
- voltage validity

The GUI also includes a field-line sketch and an ESR-versus-frequency plot so the output is not just numerical.

---

## 4. User Interface Overview

The interface is arranged so the inputs stay on the left while the outputs and plots stay on the right. The user interface updates in real time.

The main parts of the GUI are:

1. **Input Panel**
   This is where the user selects dielectric class and case size, then enters capacitance, voltage, frequency range, temperature range, and target ESR.

2. **Output Cards**
   These show the key computed values immediately after the inputs update.

3. **Tabs**
   The lower part of the interface is split into tabs:
   - `Visuals`
   - `ESR Table`
   - `Details`

4. **Warnings and Validation Feedback**
   If an input is invalid or outside an expected range, the tool reports the issue in the GUI instead of failing silently, and will typically explain the issue to the user.

### Figure 1. Visuals Tab

<img width="1280" height="799" alt="Screenshot 2026-05-02 004446" src="https://github.com/user-attachments/assets/6b65506c-8cd6-4d0c-93d5-e0e0a59ce3d1" />

Figure 1 shows the `Visuals` tab. On the left is the ESR-versus-frequency plot. On the right is the electric field sketch for the current capacitor selection.

### Figure 2. ESR Table Tab

<img width="1280" height="797" alt="Screenshot 2026-05-02 004903" src="https://github.com/user-attachments/assets/1ada3e7b-184a-4074-9be2-de2cae6f354e" />

Figure 2 shows the `ESR Table` tab. This tab lists the minimum, typical, and maximum ESR values over the selected frequency range.

### Figure 3. Details Tab

<img width="1280" height="800" alt="Screenshot 2026-05-02 004915" src="https://github.com/user-attachments/assets/4f132f08-e3f2-4d3a-89b8-717f1d6f5f48" />

Figure 3 shows the `Details` tab. This gives a longer property list for the currently selected capacitor configuration.

---

## 5. How to Use the Tool

The intended workflow is straightforward.

### Step 1. Choose the dielectric class

Select one of:

- `C0G`
- `X5R`
- `X7R`

### Step 2. Choose the case size

Select one of the supported case sizes:

- `0201`
- `0402`
- `0603`
- `0805`
- `1206`
- `1210`
- `1812`
- `2220`

### Step 3. Enter the nominal capacitance

Type in the value and then select the unit:

- `pF`
- `nF`
- `uF`
- `mF`
- `F`

### Step 4. Enter the operating conditions

Provide:

- operating voltage
- frequency minimum
- frequency maximum
- temperature minimum
- temperature maximum

### Step 5. Review the feasible ESR range

After the main inputs are validated, the program calculates the feasible ESR range at the selected maximum frequency. The ESR slider updates automatically to stay within that range.

### Step 6. Select the target ESR

Move the slider to choose a target ESR value.

### Step 7. Inspect the outputs

Use the output cards and the tabs to review:

- energy stored
- charge stored
- tan delta
- DC bias retention
- ESR trend across frequency
- detailed property values

### Step 8. Check warnings

If the user provided values violate one of the checks, the warning message area will display the issue.

---

## 6. Inputs and Validation

### 6.1 Inputs Used by the Backend

The backend workflow uses:

- `eia_class`
- `case_size`
- `nominal_capacitance_value`
- `nominal_capacitance_unit`
- `operating_voltage_V`
- `freq_min_kHz`
- `freq_max_kHz`
- `temp_min_C`
- `temp_max_C`
- `target_esr_mohm`

### 6.2 Validation Rules

The backend checks the following:

- nominal capacitance must be positive
- operating voltage must be positive
- frequency range must satisfy `0 <= freq_min_kHz < freq_max_kHz`
- temperature range must satisfy `temp_min_C < temp_max_C`
- EIA class must be one of `C0G`, `X5R`, or `X7R`
- case size must match a supported geometry
- capacitance unit must be one of `f`, `mf`, `uf`, `nf`, or `pf`

---

## 7. Outputs

The current version of the tool reports:

- nominal capacitance
- energy stored
- charge stored
- tan delta
- temperature-in-spec result
- voltage-in-spec result
- DC bias retention
- ESR range
- selected ESR
- ESR-vs-frequency data
- an expanded design detail list

---

## 8. Theory and Equations

The backend uses the equations that were identified in the design specification.

### 8.1 Capacitance

Parallel-plate capacitance:

`C = k * e0 * A / d`

Layered MLCC form:

`C = k * e0 * N * A / d`

where:

- `k` is relative permittivity
- `e0` is vacuum permittivity
- `A` is plate area
- `d` is dielectric thickness
- `N` is the number of active dielectric layers

### 8.2 Charge

`Q = C * V`

### 8.3 Stored Energy

`U = 0.5 * C * V^2`

### 8.4 Equivalent Series Resistance

`ESR = D / (2 * pi * f * C)`

where:

- `D` is dissipation factor or loss tangent
- `f` is frequency

### 8.5 Dielectric Strength Approximation

`Ebr = V / d`

as a simple breakdown-field estimate based on rated voltage and separation.

### 8.6 Visualization Assumptions

The electric field image in the GUI is only a conceptual sketch. It is useful for showing direction and layout, but it is not a full numerical field solution.

---

## 9. Worked Examples

### Example 1. X7R, 0201, 0.1 uF

Inputs:

- EIA class: `X7R`
- case size: `0201`
- nominal capacitance: `0.1 uF`
- operating voltage: `10 V`
- frequency range: `10 kHz` to `100 kHz`
- temperature range: `-55°C` to `85°C`
- target ESR: `500 mOhm`

Expected outputs:

- nominal capacitance: `1.0e-7 F`
- energy stored: `5.0e-6 J`
- charge stored: `1.0e-6 C`
- tan delta: `0.035`
- temp in spec: `True`
- voltage in spec: `True`

### Example 2. X5R, 0201, 10 uF

Inputs:

- EIA class: `X5R`
- case size: `0201`
- nominal capacitance: `10 uF`
- operating voltage: `4 V`
- frequency range: `10 kHz` to `100 kHz`
- temperature range: `-55°C` to `85°C`
- target ESR: `20 mOhm`

Expected outputs:

- nominal capacitance: `1.0e-5 F`
- energy stored: `8.0e-5 J`
- charge stored: `4.0e-5 C`
- tan delta: `0.1`
- temp in spec: `True`
- voltage in spec: `True`

### Example 3. C0G, 0201, 100 pF

Inputs:

- EIA class: `C0G`
- case size: `0201`
- nominal capacitance: `100 pF`
- operating voltage: `50 V`
- frequency range: `10 kHz` to `100 kHz`
- temperature range: `-55°C` to `85°C`
- target ESR: `15000 mOhm`

Expected outputs:

- nominal capacitance: `1.0e-10 F`
- energy stored: `1.25e-7 J`
- charge stored: `5.0e-9 C`
- tan delta: `0.001`
- DC bias retention: `100%`
- temp in spec: `True`
- voltage in spec: `True`

---

## 10. Testing and Verification

The automated tests in `test_capacitor.py` include:

- tests described in the original test document
- supplemental helper-function tests

Passed
- Python compile checks
- all tests in `test_capacitor.py`

---

## 11. Known Limitations and Assumptions

- The workflow uses embedded local lookup values inside `capacitor.py` rather than a live external dataset.
- The field visualization is illustrative, not simulation-grade.
- ESR sweep values come from the implemented backend formulas rather than direct manufacturer sweep curves.
- The backend was shaped to satisfy the provided specification and expected test cases.
- This tool is intended for engineering exploration and coursework, not manufacturing release.

---

## 12. References

### Theory and Reference Sources

1. [LibreTexts: Capacitors and Dielectrics](https://phys.libretexts.org/Courses/Joliet_Junior_College/JJC_-_PHYS_110/College_Physics_for_Health_Professions/19%3A_Electric_Potential_and_Electric_Field/19.06%3A_Capacitors_and_Dielectrics)
2. [IET Labs: Equivalent Series Resistance (ESR) of Capacitors](https://www.ietlabs.com/pdf/application_notes/035002%20Equivalent%20Series%20Resistance%20(ESR)%20of%20Capacitors%201920.pdf)
3. [Microwaves101: Capacitor Temperature Effects](https://www.microwaves101.com/encyclopedias/capacitor-temperature-effects)
4. [KYOCERA AVX: The Pros and Cons of MLCC Downsizing](https://www.kyocera-avx.com/docs/techinfo/CeramicCapacitors/The-Pros-and-Cons-of-MLCC-Downsizing.pdf)
5. [OpenStax LibreTexts: Capacitors and Capacitance](https://phys.libretexts.org/Bookshelves/University_Physics/University_Physics_(OpenStax)/University_Physics_II_-_Thermodynamics_Electricity_and_Magnetism_(OpenStax)/08%3A_Capacitance/8.02%3A_Capacitors_and_Capacitance)
6. [OpenStax LibreTexts: Energy Stored in a Capacitor](https://phys.libretexts.org/Bookshelves/University_Physics/University_Physics_(OpenStax)/University_Physics_II_-_Thermodynamics_Electricity_and_Magnetism_(OpenStax)/08%3A_Capacitance/8.04%3A_Energy_Stored_in_a_Capacitor)
7. [LibreTexts: Capacitors and Dielectrics, Georgia State University](https://phys.libretexts.org/Courses/Georgia_State_University/GSU-TM-Introductory_Physics_II_(1112)/04%3A_Electric_Potential_Energy_Electrical_Potential_or_Voltage_and_Capacitance/4.07%3A_Capacitors_and_Dielectrics)

---

## 13. Traceability Summary

- backend implementation: `capacitor.py`
- GUI implementation: `capacitor_gui.py`
- automated verification: `test_capacitor.py`
- design basis: project brief, design specification, and test-case PDFs
