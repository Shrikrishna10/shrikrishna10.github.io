+++
title = "Custom RV32IMC System-on-Chip"
date = 2026-03-19T00:00:00Z
draft = false
summary = "SystemVerilog SoC project with a 5-stage RV32IMC CPU, TileLink-UL interconnect, Boot ROM, SRAM, UART, SPI, CLINT, PLIC, and testbenches."
+++

This is a custom 32-bit RISC-V System-on-Chip written in SystemVerilog. The project packages a 5-stage in-order RV32IMC CPU with a TileLink-UL style memory system and a small set of memory-mapped peripherals suitable for simulation and Vivado-oriented FPGA synthesis work.

The design is a coursework and portfolio SoC implementation, not a silicon-proven processor. The useful part is the breadth of the integration: CPU pipeline, compressed instruction expansion, machine-mode CSRs, bus arbitration, memory map, interrupt blocks, and peripheral RTL all sit in one top-level system.

## Architecture

The CPU is organized as fetch, decode, execute, memory, and writeback stages. It targets RV32IMC, including base integer instructions, multiply/divide support, and compressed instruction decoding. Privilege support is limited to machine mode, with CSRs such as `mstatus`, `mie`, `mip`, `mtvec`, `mepc`, `mcause`, `mcycle`, and `minstret`.

Instruction and data bus ports connect into a TileLink-UL crossbar. The crossbar arbitrates the two CPU hosts and decodes transactions to Boot ROM, SRAM, CLINT, PLIC, UART, and SPI regions.

## Memory Map

| Block | Address range | Notes |
|-------|---------------|-------|
| Boot ROM | `0x0000_1000` | 4 KB ROM loaded from a hex file |
| CLINT | `0x0200_0000` | Timer and software interrupts |
| PLIC | `0x0C00_0000` | External interrupt controller |
| UART | `0x1001_0000` | 8N1 serial interface with FIFOs |
| SPI | `0x1002_0000` | Configurable SPI master |
| SRAM | `0x8000_0000` | 64 KB byte-enable SRAM |

## Peripheral Set

The UART block includes baud generation, transmit and receive paths, FIFOs, a TileLink-UL register interface, and interrupt signaling through the PLIC. The SPI block includes a clock generator, CPOL/CPHA configuration, 8-bit transfer engine, register interface, and interrupt output.

The CLINT models `mtime`, `mtimecmp`, and `msip`, while the PLIC supports multiple interrupt sources, priorities, threshold gating, and claim/complete behavior.

## Verification Work

The repository includes unit and integration testbenches for core pieces of the design:

- ALU and register-file tests.
- UART and SPI peripheral tests.
- CPU-level testbench.
- Full SoC testbench.

The testbench set is enough to exercise the main module boundaries, but it should still be treated as project verification infrastructure rather than a complete processor compliance flow.

## Next Steps

The next useful work would be adding ISA compliance tests, cleaning up boot firmware examples, running synthesis reports for a named FPGA target, and expanding integration tests around interrupts, traps, bus error handling, and peripheral software drivers.
