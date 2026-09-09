# Ultimate Keyboard ZMK Firmware

Official [ZMK Firmware](https://zmk.dev/) configuration and board definition for the **Ultimate Keyboard** (75% 84-key wireless mechanical keyboard powered by an onboard Nordic nRF52840 SoC / Ebyte E73 module).

---

## Hardware Overview & Specifications

- **Microcontroller**: Nordic nRF52840 (Ebyte E73-2G4M08S1C module)
- **Clock**: 32.768 kHz external crystal (MicroCrystal CM9V-T1A)
- **Key Matrix**: 84 keys across 7 Rows and 12 Columns (`col2row` anti-ghosting diodes)
- **Backlighting**: 84 individually addressable SK6812MINI-E RGB LEDs driven via SPI3 through a 74AHCT125 level shifter (powered from 5V USB VBUS)
- **Power Management**:
  - Integrated TP4054 Li-Ion battery charger with status indicator LED
  - AP2112K-3.3 ultra-low dropout linear regulator
  - High-impedance resistor divider on `P0.04/AIN2` ($R_7 = 2\,\text{M}\Omega$, $R_6 = 820\,\text{k}\Omega$) for accurate battery level sensing
  - External power gate MOSFET (AO3407) controlled by `P1.09`
- **Bootloader**: Adafruit nRF52 UF2 Bootloader with hardware reset button (SW85) on `P0.18`

---

## Pin Mapping Table

| Function | MCU Pad | Pin Label | Zephyr / ZMK Devicetree Reference | Notes |
| :--- | :---: | :--- | :--- | :--- |
| **Col 0 (Column 1)** | 2 | `P1.10` | `<&gpio1 10 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 1 (Column 2)** | 3 | `P0.03` | `<&gpio0 3 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 2 (Column 3)** | 4 | `P0.28` | `<&gpio0 28 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 3 (Column 4)** | 6 | `P1.13` | `<&gpio1 13 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 4 (Column 5)** | 7 | `P0.02` | `<&gpio0 2 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 5 (Column 6)** | 8 | `P0.29` | `<&gpio0 29 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 6 (Column 7)** | 9 | `P0.31` | `<&gpio0 31 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 7 (Column 8)** | 10 | `P0.30` | `<&gpio0 30 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 8 (Column 9)** | 12 | `P0.26` | `<&gpio0 26 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 9 (Column 10)** | 14 | `P0.06` | `<&gpio0 6 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 10 (Column 11)** | 16 | `P0.08` | `<&gpio0 8 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Col 11 (Column 12)** | 20 | `P0.12` | `<&gpio0 12 GPIO_ACTIVE_HIGH>` | Matrix Drive |
| **Row 0 (Row 1)** | 22 | `P0.07` | `<&gpio0 7 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | Top Function Row |
| **Row 1 (Row 2)** | 43 | `P0.10` | `<&gpio0 10 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | Number Row (NFC2 pin) |
| **Row 2 (Row 3)** | 41 | `P0.09` | `<&gpio0 9 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | QWERTY Row (NFC1 pin) |
| **Row 3 (Row 4)** | 40 | `P1.04` | `<&gpio1 4 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | Home Row |
| **Row 4 (Row 5)** | 38 | `P1.02` | `<&gpio1 2 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | Shift Row |
| **Row 5 (Row 6)** | 36 | `P1.00` | `<&gpio1 0 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | Bottom Alpha Row |
| **Row 6 (Row 7)** | 34 | `P0.22` | `<&gpio0 22 (GPIO_ACTIVE_HIGH \| GPIO_PULL_DOWN)>` | Modifiers & Space |
| **RGB LEDs (Data)** | 1 | `P1.11` | `NRF_PSEL(SPIM_MOSI, 1, 11)` | Level Shifter U4 -> DIN |
| **Battery ADC** | 18 | `P0.04` | `<&adc 2>` (AIN2 channel 2) | Divider: 2M / 820k |
| **Power Gate** | 17 | `P1.09` | `<&gpio1 9 GPIO_ACTIVE_HIGH>` | MOSFET Q2 Gate |
| **Hardware Reset** | 26 | `P0.18` | Hardware Reset | Tactile Button SW85 |
| **Crystal XL1 / XL2** | 11, 13 | `P0.00`, `P0.01` | External 32.768kHz Crystal | MicroCrystal CM9V-T1A |

---

## Flashing the Keyboard

The keyboard uses the **Adafruit nRF52 UF2 Bootloader**, which means you do not need any special programmer (like J-Link or ST-Link) to update your firmware.

### Steps to Flash:
1. Connect the keyboard to your computer via a USB-C cable.
2. Put the keyboard into **Bootloader Mode**:
   - **Method A (Hardware button)**: Quickly **double-click** the physical tactile button **SW85** on the board (or short the `RESET` test point to `GND` twice).
   - **Method B (Key combination)**: Press `Fn + B` (`&bootloader` behavior configured on Layer 1).
3. A USB mass storage drive named **`ULTIMATE_KB`** (or `NICENANO` / `NRF52BOOT`) will appear on your computer.
4. Drag and drop the compiled **`ultimate_keyboard.uf2`** file into this drive.
5. The drive will automatically unmount and the keyboard will reboot running your new firmware!

---

## Keymap Layers & Shortcuts

### Default Layer (Layer 0 - QWERTY)
A standard 75% 84-key layout:
- Function row: `Esc`, `F1` - `F12`, `PrtSc`, `Pause`, `Delete`
- Number row: `~`, `1` - `0`, `-`, `=`, `Backspace`, `Home`
- QWERTY row: `Tab`, `Q` - `P`, `[`, `]`, `\`, `Page Up`
- Home row: `Caps Lock`, `A` - `'`, `Enter`, `Page Down`
- Bottom row: `Shift`, `Z` - `/`, `Shift`, `Up`, `End`
- Modifier row: `Ctrl`, `Win`, `Alt`, `Spacebar`, `Alt`, `Fn`, `Ctrl`, `Left`, `Down`, `Right`

### Function Layer (Layer 1 - Fn)
Hold the **`Fn`** key (between Right Alt and Right Ctrl) to access:

| Key Combination | Function | Description |
| :--- | :--- | :--- |
| `Fn + 1` | `&bt BT_SEL 0` | Switch to Bluetooth Profile 1 |
| `Fn + 2` | `&bt BT_SEL 1` | Switch to Bluetooth Profile 2 |
| `Fn + 3` | `&bt BT_SEL 2` | Switch to Bluetooth Profile 3 |
| `Fn + 4` | `&bt BT_SEL 3` | Switch to Bluetooth Profile 4 |
| `Fn + 5` | `&bt BT_SEL 4` | Switch to Bluetooth Profile 5 |
| `Fn + Backspace` | `&bt BT_CLR` | Clear current Bluetooth profile bonding |
| `Fn + Q` | `&rgb_ug RGB_TOG` | Toggle RGB Backlighting ON / OFF |
| `Fn + W` | `&rgb_ug RGB_EFF` | Next RGB Effect Mode |
| `Fn + E` | `&rgb_ug RGB_EFR` | Previous RGB Effect Mode |
| `Fn + R` | `&rgb_ug RGB_HUI` | Increase RGB Hue |
| `Fn + T` | `&rgb_ug RGB_HUD` | Decrease RGB Hue |
| `Fn + Y` | `&rgb_ug RGB_SAI` | Increase RGB Saturation |
| `Fn + U` | `&rgb_ug RGB_SAD` | Decrease RGB Saturation |
| `Fn + I` | `&rgb_ug RGB_BRI` | Increase RGB Brightness |
| `Fn + O` | `&rgb_ug RGB_BRD` | Decrease RGB Brightness |
| `Fn + PrtSc` | `&kp C_MUTE` | Mute Audio |
| `Fn + Pause` | `&kp C_VOL_DN` | Volume Down |
| `Fn + Delete` | `&kp C_VOL_UP` | Volume Up |
| `Fn + Up` | `&kp C_PREV` | Previous Track |
| `Fn + End` | `&kp C_NEXT` | Next Track |
| `Fn + Space` | `&kp C_PP` | Play / Pause Media |
| `Fn + B` | `&bootloader` | Enter DFU Bootloader mode for flashing |
| `Fn + N` | `&sys_reset` | Soft Reboot the keyboard |

---

## How to Customize Your Keymap

There are 3 easy ways to update your keymap:

### Method 1: Edit `config/ultimate_keyboard.keymap`
1. Open [`config/ultimate_keyboard.keymap`](config/ultimate_keyboard.keymap) in any text editor.
2. Edit keycodes using standard [ZMK Keycodes](https://zmk.dev/docs/codes).
3. Commit and push your changes to GitHub:
   ```bash
   git add firmware/config/ultimate_keyboard.keymap
   git commit -m "Update keymap"
   git push origin main
   ```
4. GitHub Actions will automatically compile a new `.uf2` file. Go to the **Actions** tab on your GitHub repository, download the new firmware artifact, and flash it!

### Method 2: ZMK Studio (Real-Time Live Web Editing)
This firmware includes `CONFIG_ZMK_STUDIO=y`. You can connect your keyboard via USB to Google Chrome or Microsoft Edge and open:
👉 **[https://zmk.studio](https://zmk.studio)**
You can click on any key to remap it, and changes apply **instantly in real time** without having to reflash!

### Method 3: Nick Coutsos Keymap Editor (Visual GUI)
1. Visit **[https://nickcoutsos.github.io/keymap-editor/](https://nickcoutsos.github.io/keymap-editor/)**
2. Connect your GitHub account and select your `ultimate-keyboard` repository.
3. Use the interactive GUI to change keys, add layers, and click **Save**. It will automatically commit the changes to your repository and trigger the build!

---

## How to Build the Firmware

### Option 1: GitHub Actions (Recommended & Automated)
Every time you push changes to your repository on GitHub, the workflow in [`.github/workflows/build.yml`](../.github/workflows/build.yml) automatically runs in the cloud using the official `zmkfirmware/zmk-build-arm:stable` container.
- To download your built firmware:
  1. Open your repository on GitHub.
  2. Click the **Actions** tab.
  3. Click on the latest workflow run.
  4. Scroll down to **Artifacts** and download `ultimate_keyboard_firmware.zip`.
  5. Unzip to get `ultimate_keyboard.uf2` and flash it!

### Option 2: Local Build with Docker
If you have Docker installed on your computer, you can build locally in seconds:
```bash
docker run --rm -it \
  -v "${PWD}:/workspace" \
  -w /workspace \
  zmkfirmware/zmk-build-arm:stable \
  bash -c "
    west init -l firmware/config
    west update
    west zephyr-export
    west build -s zmk/app -d build -b ultimate_keyboard -- \
      -DZMK_CONFIG='/workspace/firmware/config' \
      -DZMK_EXTRA_MODULES='/workspace/firmware'
    cp build/zephyr/zmk.uf2 /workspace/firmware/ultimate_keyboard.uf2
  "
```

### Option 3: Local Build with West CLI
If you have Zephyr SDK and Python installed:
```bash
cd firmware
west init -l config
west update
west zephyr-export
west build -s zmk/app -d build -b ultimate_keyboard -- -DZMK_CONFIG="$(pwd)/config" -DZMK_EXTRA_MODULES="$(pwd)"
cp build/zephyr/zmk.uf2 ./ultimate_keyboard.uf2
```
