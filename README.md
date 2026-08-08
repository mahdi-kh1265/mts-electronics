# mts-electronics

Analog RF, photodetector, and demodulation electronics for POSM's modulation-transfer spectroscopy (MTS) laser-locking system.

This repository contains two custom PCBs:

1. **`mts-clk`** — 10 MHz RF distribution and phase-control board for the EOM drive and MTS demodulation local oscillator.
2. **`mts-demod`** — photodiode front end, RF conditioning, analog mixer demodulation, and baseband error-signal generation.

The boards form the analog front end of POSM's rubidium MTS frequency lock.

---

## System Overview

# 1. `mts-clk` — 10 MHz Clock / Phase-Control Board

`mts-clk` takes a common 10 MHz RF source and generates the two coherent RF signals needed by the spectroscopy system:

* **EOM path** — drives the modulation chain associated with the electro-optic modulator.
* **DEMOD path** — provides the coherent local oscillator used by the analog MTS mixer.

Using a common source preserves coherence between modulation and demodulation.

# 2. `mts-demod` — Photodiode and Analog Demodulator

`mts-demod` converts the optical MTS signal into a low-frequency analog error signal suitable for digitization and feedback.

The board includes:

* FDS010 photodiode interface
* reverse-bias circuitry
* OPA858 transimpedance amplifier
* OPA695 second-stage RF amplifier
* 25 MHz RF cleanup filters
* RF splitting / diagnostic outputs
* conditioned 10 MHz LO path
* Mini-Circuits ADE-1+ mixer
* Mini-Circuits PLP-5+ post-mixer low-pass filter
* raw and demodulated monitor outputs

# 3. Repository Structure

```text
mts-electronics/
│
├── mts-clk/
│   ├── mts-clk.kicad_sch
│   ├── mts-clk.kicad_pcb
│   ├── mts-clk.kicad_pro
│   ├── libraries/
│   └── gerbers/
│
├── mts-demod/
│   ├── mts-demod.kicad_sch
│   ├── mts-demod.kicad_pcb
│   ├── mts-demod.kicad_pro
│   └── libraries/
│
├── LICENSE
└── README.md
```

The projects are currently stored in **KiCad 10** format.

---

# 4. Major Components

| Function                            | Part                      | Board       |
| ----------------------------------- | ------------------------- | ----------- |
| RF power splitter                   | Mini-Circuits TCP-2-10X   | `mts-clk`   |
| EOM-path RF amplifier               | ADI LTC6433-15            | `mts-clk`   |
| DEMOD-path RF amplifier             | ADI LTC6433-15            | `mts-clk`   |
| Voltage-controlled RF phase shifter | Mini-Circuits SCPHS-13.6+ | `mts-clk`   |
| Phase-control buffer                | TI OPA197                 | `mts-clk`   |
| Photodiode                          | Thorlabs FDS010           | `mts-demod` |
| First-stage TIA                     | TI OPA858                 | `mts-demod` |
| Second-stage amplifier              | TI OPA695                 | `mts-demod` |
| LO gain block                       | Mini-Circuits GVA-81      | `mts-demod` |
| Mixer                               | Mini-Circuits ADE-1+      | `mts-demod` |
| Post-mixer LPF                      | Mini-Circuits PLP-5+      | `mts-demod` |

Refer to the KiCad schematics for current component values and complete BOM information.

---

# 5. Bring-Up Sequence

These boards should be characterized independently before being connected to the complete laser lock.

## `mts-clk`

1. Verify resistance from each supply rail to ground before applying power.
2. Power the board with no external RF connections.
3. Verify all supply rails and amplifier bias conditions.
4. Apply a known 10 MHz RF input.
5. Measure the EOM output with an oscilloscope or spectrum analyzer.
6. Measure the DEMOD output.
7. Sweep the analog phase-control voltage.
8. Verify phase variation at 10 MHz relative to the EOM output.
9. Verify output amplitude remains within the intended range across the useful phase-control range.
10. Characterize phase versus control voltage and save the calibration curve.

## `mts-demod`

1. Verify all power rails before installing/connecting sensitive external hardware.
2. Verify photodiode reverse bias.
3. Illuminate the FDS010 with a controlled optical signal.
4. Measure the OPA858 TIA output.
5. Verify the OPA695 second-stage gain.
6. Verify the 25 MHz cleanup-filter response.
7. Apply a known 10 MHz DEMOD/LO signal.
8. Verify the GVA-81 LO chain.
9. Verify adequate LO drive at the ADE-1+.
10. Observe the raw mixer IF.
11. Verify the PLP-5+ output.
12. Sweep relative RF/LO phase and confirm the expected quadrature dependence.
13. Only after analog characterization, connect the ADC / FPGA system.

---

# 10. Characterization Data to Add

The schematic captures the intended design. Measured hardware performance should be added separately once boards are assembled.

Useful measurements include:

### CLK board

* EOM output power at 10 MHz
* DEMOD output power at 10 MHz
* EOM-to-DEMOD relative phase range
* phase versus control voltage
* amplitude versus phase-control voltage
* harmonic distortion
* broadband noise
* output return loss / matching

### DEMOD board

* TIA transimpedance
* TIA noise spectral density
* complete photodiode-path gain
* 25 MHz filter response
* mixer conversion gain/loss
* LO level at ADE-1+
* baseband transfer function
* analog error-signal noise
* output offset
* usable MTS discriminator slope
* channel saturation limits

Measured data should eventually be stored under a dedicated directory such as:

```text
measurements/
├── mts-clk/
│   ├── phase_vs_control_voltage.csv
│   ├── output_spectrum/
│   └── network_analyzer/
│
└── mts-demod/
    ├── tia_noise/
    ├── rf_transfer_function/
    ├── mixer_characterization/
    └── mts_error_signal/
```
# License

Released under the license included in [`LICENSE`](LICENSE).

---

## POSM

**Photonics and Optical Sciences Club at Minnesota**
University of Minnesota

[POSM website](https://posmphotonics.org/)
