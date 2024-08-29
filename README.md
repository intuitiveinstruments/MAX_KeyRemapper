
# Max KeyRemapper patch for Intuitive Instruments Exquis MPE Controller

This MAX patch allow to remap MIDI notes of Exquis' keys and set the LEDs under the keys. It controls the Exquis using MIDI SysEx messages. The Exquis controller offers a "slave mode" that allows you to send and receive specific SysEx messages for controlling its keys, buttons, and encoders.

## Requirements

- **MAX/MSP**: Version 8 or higher
- **Exquis MPE Controller**

### ⚠️ Warning

The SysEx messages exposed in this documentation are based on the current firmware version of the Exquis controller. **Intuitive Instruments may change these SysEx messages in future firmware updates**. As a result, we cannot guarantee that your patches or modifications will remain functional after such updates. Use this information at your own risk, and be aware that future changes to the Exquis firmware may break compatibility with your custom setups or hacks. We are not responsible for any issues that arise from such changes.

## Overview of the Max patch
### 1. **General principle**
The patch requires a permanent connection with the Exquis, maintained by sending an "active sensing" SysEx message to the Exquis at least every 300 ms. This puts the Exquis into "slave mode" and allows SysEx messages to be sent to:
- Redefine the MIDI note output by each Exquis key
- Set the color of the LED under each key
If the "active sensing" message is not sent, the Exquis will revert to "standalone mode".

The patch uses the `./Ressources/` folder to populate a list of files in a dropdown menu. Simply manage your files in the correct folder to easily access them through the dropdown menu.

### 2. **MIDI Routing**
The MIDI routing will depend on the platform you use.

**Mac OS:**
Simply choose "Exquis" as the MIDI input of your DAW or virtual synths. Ensure that the MIDI output of the Max patch is set to "Exquis" in the "Exquis control start/stop" section of the patch.

**Windows:**
If you don't use a virtual MIDI loopback or virtual MIDI port, follow these steps:

1. Connect the Exquis to your computer via USB.
2. Launch your DAW or virtual synth, and select "Exquis" as the MIDI input.
3. Launch the Max patch and establish the connection with the Exquis.

If you reverse steps 2 and 3, you might not be able to select "Exquis" as a MIDI input for your DAW or synth because it's already being used by Max.

If you use a virtual MIDI loopback or virtual MIDI port, ensure you redirect the Exquis output to your DAW/synths and the output of the Max patch to the Exquis input.

## Overview of MIDI SysEx Messages

### 1. **Active Sensing (Slave Mode Activation)**

To activate the Exquis in "slave mode," you must send an "active sensing" SysEx message every 300 ms. If this message is not sent, the Exquis will revert to "standalone mode."

**SysEx Message:**
```
F0 00 21 7E F7
```

**Byte Breakdown:**
- `F0` (240): Start of SysEx
- `00 21 7E`: Manufacturer ID for Dualo
- `F7` (247): End of SysEx

### 2. **Set MIDI Note Number on a Key**

To assign a specific MIDI note number to a key on the Exquis:

**SysEx Message:**
```
F0 00 21 7E 04 noteId noteNumber F7
```

- `noteId`: The ID of the key you want to assign (in hex).
- `noteNumber`: The MIDI note number to assign (in hex).

**Example:**
```
240 0 33 126 4 40 25 247
```
This assigns MIDI note number 25 (decimal) to key ID 40 (decimal).

### 3. **Set a Color on a Key**

To set an RGB color on a key:

**SysEx Message:**
```
F0 00 21 7E 03 noteId r g b F7
```

- `noteId`: The ID of the key.
- `r`, `g`, `b`: Red, Green, Blue values (0-127, 7 bits each).

### 4. **Set a Color on a Button**

To set an RGB color on a button:

**SysEx Message:**
```
F0 00 21 7E 07 buttonId r g b F7
```

- `buttonId`: The ID of the button.
- `r`, `g`, `b`: Red, Green, Blue values (0-127, 7 bits each).

### 5. **Set a Color on an Encoder**

To set an RGB color on an encoder:

**SysEx Message:**
```
F0 00 21 7E 09 knobId r g b F7
```

- `knobId`: The ID of the encoder.
- `r`, `g`, `b`: Red, Green, Blue values (0-127, 7 bits each).

## Messages Sent by Exquis

### 1. **Button Click**

Exquis sends a message when a button is pressed or released:

**SysEx Message:**
```
F0 00 21 7E 08 buttonId buttonState F7
```

- `buttonId`: The ID of the button.
- `buttonState`: `1` for press, `0` for release.

**Example:**
```
240 0 33 126 8 6 1 247
```
This indicates that the "octave -" button (ID 6) is pressed.
