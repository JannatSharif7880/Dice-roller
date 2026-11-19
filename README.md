# Microcontroller-Free Electronic Dice Roller

An electronic dice roller built only from basic logic chips, with no microcontroller and no code. Press and hold the button and the display cycles quickly through the digits. Let go, and it stops on a number from 0 to 6.

Course project for **CSE350: Digital Electronics and Pulse Techniques**, Department of Computer Science and Engineering, BRAC University, Dhaka.

![Breadboard build](images/breadboard-top.jpg)

## How it works

A microcontroller would simply call a random-number function. Without one, this circuit uses the "stopwatch" method instead: a fast clock races against a slow human reaction. Nobody can time a button release precisely enough to pick a number, so the result is unpredictable.

1. **Clock:** an NE555 timer in astable mode produces clock pulses while the button is held down.
2. **Counter:** an SN7490 counts the pulses in binary-coded decimal (BCD).
3. **Reset at 7:** two NAND gates from an SN74LS10 detect when the count reaches 7 and reset the counter immediately, so it loops through 0 to 6 instead of 0 to 9.
4. **Display:** an SN74HC47 decoder converts the BCD value into segments on a common-anode seven-segment display.

## Components

| Part | Chip | Role |
|------|------|------|
| U1 | NE555 | Astable clock |
| U2 | SN7490 | BCD counter |
| U3 | SN74LS10 | Detects the count of 7 and resets the counter |
| U4 | SN74HC47 | Seven-segment decoder and driver |
| D1 | Seven-segment display (common anode) | Shows the result |

![Schematic](images/schematic.png)

## From simulation to breadboard

We designed and tested the full circuit in **Proteus** first. In simulation, it cycled through the digits and stopped on a random value between 0 and 6 when the button was released. We then built the same design on a breadboard with the same part numbers.

![Proteus simulation](images/proteus-simulation.png)

## Problems we found and fixed

The breadboard version did not work at first, and neither fault could have appeared in the simulation.

- **The display only showed 0 and 2.** In BCD, 0 is `0000` and 2 is `0010`, so only one bit was changing. That pattern told us the decoder's other three inputs were stuck low. They turned out to be loose breadboard connections, which we fixed.
- **Random glitches in the reset logic.** We had left the inputs of the unused third NAND gate on the SN74LS10 unconnected. Floating TTL inputs pick up electrical noise, and the noise affected the two gates we were using. Tying those inputs to a fixed supply rail fixed it.

The main lesson was that a circuit that works in simulation can still fail on real hardware. Studying the pattern of wrong outputs helped us find each fault without rewiring everything.

## Possible improvements

- Add decoupling capacitors across each chip's power pins to reduce switching noise.
- Debounce the roll button so it can't create extra clock pulses.
- Change the reset logic so the display shows 1 to 6, like a real die.
- Build a two-dice version by duplicating the circuit and sharing the clock.
- Move the circuit to a soldered board or PCB so the connections are reliable.

## Repository contents

- `report/` – full project report (PDF)
- `simulation/` – Proteus project file
- `images/` – schematic, simulation screenshot and breadboard photos

## Team

- Muskan Ashraf Apia
- Jannat Sharif
- Mariyam Mahbub
- Sahela Khan Retu

## References

1. Texas Instruments, *xx555 Precision Timers* (NE555), datasheet SLFS022K.
2. Texas Instruments, *SN7490A / SN74LS90 Decade, Divide-by-Twelve and Binary Counters*, datasheet SDLS940A.
3. Texas Instruments, *Triple 3-Input Positive-NAND Gates* (SN74LS10), datasheet SDLS035A.
4. Texas Instruments, *BCD-to-Seven-Segment Decoders/Drivers* (SN7447A), datasheet SDLS111.
5. P. Horowitz and W. Hill, *The Art of Electronics*, 3rd ed., Cambridge University Press, 2015.
