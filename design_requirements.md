# Requirements

## Scope

The primary purpose of this system is to record and store Digitalker ROM audio for historical archiving. This will be done by using the National Semiconductor Digitalker MM54104 speech processing chip to read speech ROMs and output the audio stream to a recording device. The recording device will store each word on the ROMs as a separate audio file, which can then be archived.

## General Requirements

- The system shall contain all components required for playing, recording, and storing speech ROMs on a single PCB.
- The system shall be capable of storing and playing all 13 of the known ROMs[^1] on-board.
- The system shall be powered with a single external power supply.
- The system shall expose an external data interface (UART, USB CDC, RS-232, etc.) to allow an external PC to interface with the system and issue commands.   

## Audio Requirements

- The system shall provide two distinct audio output paths:
  - A dedicated external speaker output.
  - A dedicated on-board recording output.
- The system shall record and store each ROM word as an independant 48kHz, 16-bit, mono .WAV file.
- Recorded .WAV files shall be saved to an on-board microSD card.

[^1]: Known ROMs (with availible dumps) are DT1052, SSR1/SSR2, SSR5/SSR6, Jameco JE-520, Genesis Alarm Panel A20400, Duofone, Sensaphone 1104, Dynamic Logic LTD Elevator Panel, RS Components Type-2 (MM52664SJQR), RS Components Type-4 (MM52664SJQT), Faceoff Pinball, Scorpion Pinball, and Harem Pinball (~256KB total).
