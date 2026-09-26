# Ultimate-keyboard

A custom **75% (84-key)** mechanical keyboard built from the ground up with **Bluetooth 5.0 wireless & wired USB-C connectivity**, **individually addressable per-key RGB backlighting**, **Kailh MX hot-swap sockets**, and **integrated LiPo battery charging**.

It uses the [nrfmicro](https://github.com/joric/nrfmicro) microcontroller architecture as inspiration. Basically, instead of soldering a separate development board onto the keyboard, I copied the microcontroller schematics, put the **Nordic nRF52840 SoC** (via Ebyte E73 module) directly onto the keyboard's PCB, added battery charging and power management circuitry, and routed the entire PCB myself! At the end of the day, the IC lives right on the keyboard PCB with ample room to route everything cleanly.

---

## 3D Renders & Showcase

|                     Top View                      |              Isometric Side View               |                    Bottom View                     |
| :-----------------------------------------------: | :--------------------------------------------: | :------------------------------------------------: |
| ![Top View](media/keyboard%20from%20above%23.png) | ![Side View](media/keyboard%20from%20side.png) | ![Bottom View](media/keyboard%20from%20bottom.png) |

|                PCB Top Layer                |                 PCB Bottom Layer                 |
| :-----------------------------------------: | :----------------------------------------------: |
| ![PCB Top](media/keyboaerd%20top%20pcb.png) | ![PCB Bottom](media/keyboard%20bottom%20PCB.png) |

---

## Project Directory Map

```
ultimate-keyboard/
├── .github/
│   └── workflows/
│       └── build.yml               # Automated ZMK CI build workflow
├── boards/hashim/ultimate_keyboard/ # Zephyr HWMv2 board definition
│   ├── board.yml                   # Board metadata (SoC: nrf52840, variant: zmk)
│   ├── Kconfig.ultimate_keyboard   # Board selection & ZMK_BOARD_COMPAT
│   ├── Kconfig.defconfig           # Name & bootloader retention settings
│   ├── ultimate_keyboard.zmk.yml   # ZMK hardware metadata catalog
│   ├── ultimate_keyboard_nrf52840_zmk.dts # Devicetree definition (matrix, ADC, SPI RGB)
│   └── ultimate_keyboard_nrf52840_zmk_defconfig # Base Kconfig drivers
├── config/
│   ├── ultimate_keyboard.keymap    # 84-key QWERTY + Function layers
│   ├── ultimate_keyboard.conf      # Bluetooth TX power, sleep, RGB underglow
│   └── west.yml                    # Manifest
├── firmware/
│   ├── boards/hashim/ultimate_keyboard/ # Board definition mirror
│   ├── config/                     # Keymap and config mirror
│   ├── build.yaml                  # Firmware build matrix
│   └── ultimate_keyboard.uf2       # Compiled functional flash file (ready to flash)
├── hardware/
│   ├── nrfmicro.kicad_sch
│   ├── nrfmicro.kicad_pcb
│   ├── nrfmicro.kicad_pro
├── exports/
│   ├── gerbers.zip
│   └── others/
│       ├── nrfmicro.csv
│       ├── nrfmicro.step
│       └── nrfmicro board.step
├── media/
├── build.yaml
├── keyboard-layout.json
├── upper plate.dxf
├── journal.md
└── README.md
```

---

## Bill of Materials (BOM)

### 1. Integrated Circuits & Semiconductors

| Designator | Value / Part                                                                                                                  | Package / Footprint          | Qty | Description                                                              |
| :--------- | :---------------------------------------------------------------------------------------------------------------------------- | :--------------------------- | :-: | :----------------------------------------------------------------------- |
| U1         | **[E73-2G4M08S1C](https://www.ebyte.com/en/product-view-news.html?id=445)**                                                   | E73-2G4M08S1C-52840          |  1  | Nordic nRF52840 Bluetooth 5.0 & USB 2.0 SoC Module with PCB Antenna      |
| U2         | **[AP2112K-3.3](https://lcsc.com/product-detail/Low-Dropout-Regulators-LDO_Diodes-Incorporated-AP2112K-3-3TRG1_C51118.html)** | SOT-23-5                     |  1  | 3.3V 600mA Ultra-Low Dropout (LDO) Linear Voltage Regulator              |
| U3         | **[TP4054](https://lcsc.com/product-detail/Battery-Management-ICs_TP4054_C32574.html)**                                       | SOT-23-5                     |  1  | Standalone Linear Li-Ion Battery Charger IC                              |
| U4         | **[74AHCT125](https://lcsc.com/product-detail/Buffers-Drivers-Receivers-Transceivers_Nexperia-74AHCT125D-118_C137151.html)**  | SOIC-14 (3.9x8.7mm, P1.27mm) |  1  | Quad Buffer / 3.3V to 5V Logic Level Shifter for RGB LEDs                |
| Q1, Q2     | **[AO3407](https://lcsc.com/product-detail/MOSFETs_Alpha-Omega-Semicon-AO3407_C13184.html)**                                  | SOT-23                       |  2  | P-Channel 30V MOSFET (Power path switching & battery reverse protection) |
| D100       | **[1N5819](https://lcsc.com/product-detail/Schottky-Barrier-Diodes-SBD_1N5819W_C169344.html)**                                | D_SOD-323F                   |  1  | Schottky Barrier Rectifier Diode (Power OR-ing)                          |
| D200       | **RED LED**                                                                                                                   | LED_0603_1608Metric          |  1  | Red 0603 SMD LED (Battery charge indicator)                              |

### 2. Key Matrix Diodes & Addressable RGB LEDs

| Designator   | Value / Part                                                                                                   | Package / Footprint | Qty | Description                                               |
| :----------- | :------------------------------------------------------------------------------------------------------------- | :------------------ | :-: | :-------------------------------------------------------- |
| D1 – D84     | **[1N4148WT](https://lcsc.com/product-detail/Switching-Diodes_1N4148W_C2128.html)**                            | D_SOD-123           | 84  | High-Speed Switching Diodes (Anti-Ghosting / NKRO Matrix) |
| LED1 – LED84 | **[SK6812MINI-E](https://lcsc.com/product-detail/Light-Emitting-Diodes-LED_HOUEN-SK6812MINI-E_C2890035.html)** | MX_SK6812MINI-E_REV | 84  | Reverse-Mount Individually Addressable RGB LEDs           |

### 3. Kailh MX Hot-Swap Sockets

| Designator        |                                       Key Size                                        | Footprint                 |  Qty   | Target Keys                                           |
| :---------------- | :-----------------------------------------------------------------------------------: | :------------------------ | :----: | :---------------------------------------------------- |
| SW2, SW29, ...    | **[1.00u Socket](https://www.aliexpress.com/w/wholesale-kailh-hot-swap-socket.html)** | SW_Hotswap_Kailh_MX_1.00u |   73   | Standard 1u Alpha, Number, Function & Navigation keys |
| SW75, SW76, SW77  |                                   **1.25u Socket**                                    | SW_Hotswap_Kailh_MX_1.25u |   3    | Left Ctrl, Left Win, Left Alt                         |
| SW45, SW32        |                                   **1.50u Socket**                                    | SW_Hotswap_Kailh_MX_1.50u |   2    | Tab, Backslash (\|)                                   |
| SW47, SW72        |                                   **1.75u Socket**                                    | SW_Hotswap_Kailh_MX_1.75u |   2    | Caps Lock, Right Shift                                |
| SW30              |                                   **2.00u Socket**                                    | SW_Hotswap_Kailh_MX_2.00u |   1    | Backspace                                             |
| SW59, SW61        |                                   **2.25u Socket**                                    | SW_Hotswap_Kailh_MX_2.25u |   2    | Left Shift, Enter                                     |
| SW78              |                                   **6.25u Socket**                                    | SW_Hotswap_Kailh_MX_6.25u |   1    | Spacebar                                              |
| **Total Sockets** |                                                                                       |                           | **84** | Full 75% layout coverage                              |

### 4. Connectors, Switches & Crystal

| Designator | Value / Part                                                                                                                                | Package / Footprint                   | Qty | Description                                                    |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------ | :-: | :------------------------------------------------------------- |
| J1         | **[USB-C Receptacle 16-pin](https://lcsc.com/product-detail/USB-Connectors_SHOU-HAN-TYPE-C-16PIN_C319133.html)**                            | USB-C_C168688                         |  1  | USB Type-C Receptacle (16-Pin)                                 |
| J2         | **Conn_01x04**                                                                                                                              | connector_swd                         |  1  | 4-Pin SWD Header (VDD, SWDIO, SWCLK, GND)                      |
| J3         | **Conn_01x02**                                                                                                                              | PinSocket_1x02_P1.27mm_Vertical       |  1  | 2-Pin Battery Header (for LiPo battery)                        |
| SW85       | **[Tactile Switch](https://lcsc.com/product-detail/Tactile-Switches_ALPS-ALPINE-SKRK-series_C2665793.html)**                                | SW_Push_SPST_NO_Alps_SKRK             |  1  | Tactile Push Button (Reset / Bootloader mode)                  |
| RESET      | **TestPoint**                                                                                                                               | TestPoint_THTPad_1.0x1.0mm_Drill0.5mm |  1  | Hardware Reset Test Point                                      |
| X1         | **[32.768kHz Crystal](https://lcsc.com/product-detail/Crystals_Micro-Crystal-Switzerland-CM9V-T1A-32-768kHz-7pF-20PPM-TA-QC_C347311.html)** | CM9V-T1A-2Pin_1.6x1.0mm               |  1  | MicroCrystal CM9V-T1A low-frequency crystal for BLE sleep mode |

### 5. Resistors (SMD 0603)

| Designator |   Value   | Footprint         | Qty | Function / Notes                                               |
| :--------- | :-------: | :---------------- | :-: | :------------------------------------------------------------- |
| R1         | **100kΩ** | R_0603_1608Metric |  1  | Pull-up / Gate bias resistor                                   |
| R2, R3     | **5.1kΩ** | R_0603_1608Metric |  2  | USB Type-C CC1 / CC2 pull-down resistors (5V host negotiation) |
| R5         | **RPROG** | R_0603_1608Metric |  1  | Charge current setting resistor for TP4054                     |
| R6         | **820kΩ** | R_0603_1608Metric |  1  | Voltage divider bias resistor                                  |
| R7, R9     |  **2MΩ**  | R_0603_1608Metric |  2  | High-impedance battery voltage monitoring divider              |
| R8         |  **1kΩ**  | R_0603_1608Metric |  1  | Current limiting resistor for indicator LED                    |

---

## Wiring Diagram

![Completed wiring diagram](/exports/diagrams/keyboard_wiring_diagram.jpg)
see in [pdf](/exports/diagrams/keyboard_wiring_diagram.pdf)

## Schematics & Wiring Diagrams

![Completed Schematic](media/schematics.png)

### Hardware Wiring & Connections Diagram

A clean, Arduino-style pictorial wiring diagram on a plain white background with real component 3D renders (PCB, LiPo battery, USB-C, ST-Link SWD programmer, Kailh MX socket) and color-coded curved wiring is available in:

- **High-Res 300 DPI Image (10,417 × 5,834 px)**: [`exports/diagrams/keyboard_wiring_diagram.jpg`](exports/diagrams/keyboard_wiring_diagram.jpg)
- **Vector PDF**: [`exports/diagrams/keyboard_wiring_diagram.pdf`](exports/diagrams/keyboard_wiring_diagram.pdf)

![Bootloader Circuit](media/bootloader%20button.png)
_This is the physical tactile button (SW85) to press to enter bootloader mode._

---

## Firmware (ZMK)

The keyboard runs native [ZMK Firmware](https://zmk.dev/) built on the Zephyr RTOS with full support for Bluetooth Low Energy 5.0, USB HID, low-power deep sleep, battery level telemetry, and individually addressable RGB backlighting.

A pre-compiled, tested, ready-to-flash binary is available at: **[`firmware/ultimate_keyboard.uf2`](firmware/ultimate_keyboard.uf2)**

## How to Build the Firmware

### GitHub Actions (Recommended & Automated)

This repository includes a GitHub Actions workflow in [`.github/workflows/build.yml`](.github/workflows/build.yml) configured with ZMK's official build matrix. Every push to `main` automatically triggers a build in the cloud.

1. Edit your keymap in [`config/ultimate_keyboard.keymap`](config/ultimate_keyboard.keymap) or settings in [`config/ultimate_keyboard.conf`](config/ultimate_keyboard.conf).
2. Commit and push your changes:
3. Once the workflow run completes, click into the run, scroll to **Artifacts**, and download `ultimate_keyboard.zip`.
4. Extract the `.uf2` file and flash it!
   > (enter bootloader mode by holding the dedicated button beneath space bar)

---

## Assembly

After getting the PCB and the components, you can assemble the keyboard by following these steps:

- first of all solder the MCU and the ICs surrounding it on the top side of the PCB with a heat gun.
- then solder the usbc port and the boot button and the battery connections header(you can solder the wires directly to the pads if you want).
- then solder the diodes, rgb leds and switch sockets along with everything remaining on the bottom side of the PCB.
- connect the battery, flash the firmware, put the switches in and then you can test the keyboard and make sure everything is working properly.

---

### AI Usage Declaration

I used AI for research, general queries and for asking question like using it instead of google, and formatting the markdown documentation and BOM tables. and fixing firmware as well.
