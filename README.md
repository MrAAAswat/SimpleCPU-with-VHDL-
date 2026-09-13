# SimpleCPU-with-VHDL

A simple **4-stage pipelined CPU** implemented in VHDL on an FPGA (Altera DE-series board),
capable of executing basic arithmetic and logic operations. The CPU includes a small
register file with **four 8-bit registers** and uses **RS** and **RT** fields to select
source and destination operands.

> Lab 8 details a finite-state machine (FSM) solution, whereas Lab 9 introduces pipelining
> with consecutive instructions. The VHDL source and further details can be found in the
> documents.

## Instruction Set

| Instruction | Opcode | Rs (2-bit) | Rt (2-bit) | Usage        | Result              |
|-------------|--------|------------|------------|--------------|---------------------|
| Load        | 0000   | ✓          | ✓          | Load Rs, Data| Rs ← Data           |
| Move        | 0001   | ✓          | ✓          | Move Rs, Rt  | Rs ← Rt             |
| Add         | 0010   | ✓          | ✓          | Add Rs, Rt   | Rs ← Rs + Rt        |
| And         | 0011   | ✓          | ✓          | And Rs, Rt   | Rs ← Rs & Rt        |
| Sub (A-B)   | 0101   | ✓          | ✓          | Sub Rs, Rt   | Rs ← Rs − Rt        |
| Sub (B-A)   | 1001   | ✓          | ✓          | Sub Rs, Rt   | Rs ← Rt − Rs        |
| Nor         | 0110   | ✓          | ✓          | Nor Rs, Rt   | Rs ← Rs NOR Rt      |
| Slt         | 0100   | ✓          | ✓          | Slt Rs, Rt   | Rs = 1 if Rs < Rt else 0 |
| Div         | 1000   | ✓          | ✓          | Div Rs, Rt   | Rs ← Rs / Rt        |
| NOP         | 1111   | —          | —          |              | No operation        |

## Overview

This lab focuses on designing a simple 4-stage pipelined CPU. Instructions are input
manually through switches and move through the pipeline stages: **fetching, decoding,
execution, and write-back**. The design takes advantage of VHDL's concurrent `process`
behavior to simulate the pipeline structure. The register contents required by each stage
are stored as **signals** for data transfer, and a **valid buffer** controls the
activation/deactivation of each stage.

- **IF (Instruction Fetch):** Reads the instruction according to the Lab 8 port map.
- **ID (Instruction Decode):** Converts the Rs & Rt addresses fetched from the instruction into data.
- **EXE (Execute):** Computes the result from the data (Rs & Rt) according to the opcode.
- **WB (Write-Back):** Writes the result back into the register pointed to by Rs (LED lights up on write).

## I/O

| Signal  | Direction | Description                                            |
|---------|-----------|--------------------------------------------------------|
| `clk`   | in        | Clock (button input)                                   |
| `Opcode`| in        | 4-bit operation code (switches)                        |
| `Rs`    | in        | 2-bit source/destination register (switches)           |
| `Rt`    | in        | 2-bit source register (switches)                       |
| `Data`  | in        | 8-bit immediate data (switches)                        |
| `HEX0–HEX5` | out   | 7-segment displays (input data + Rs / Rt register values in real time) |
| `LEDG`  | out       | Pipeline stage status (`valid` bits, one LED per stage) |

## Known Issues / Notes

1. Opcode `1111` is a NOP.
2. **Div** is not implemented (left as `TODO`).
3. **Hazard / forwarding logic** is not yet implemented (data hazards between consecutive instructions are not handled).

## Test Results

- **[Test 1]** `Load 00, 0x89` → `Move 01, 00`: register r0 loaded with `0x89`, then copied to r1.
- **[Test 2]** `Load 00, 0x08` → `Add 00, 01`: `r0 = 0x08 + 0x89 = 0x91`.

## Source

The complete VHDL implementation (`SampleCPU` entity) is included in the report documents.

## Authors

- Group 34
- 110590042 柳艾德
- 112590002 林羿安

*Microprocessor Systems (微算機系統) – 2025*
