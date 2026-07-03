+++
title = "Runtime Reconfiguration of a RISC-V Co-Processor"
date = 2025-07-13T01:11:59Z
draft = false
summary = "Group capstone project on Dynamic Partial Reconfiguration for hot-swapping RISC-V custom function units on FPGA."
+++

This was a group capstone project on Dynamic Partial Reconfiguration (DPR) for FPGA systems. The team built a runtime-reconfigurable RISC-V co-processor path where accelerator modules can be swapped without reloading the full FPGA bitstream or resetting the processor.

The design integrates Google's CFU Playground framework with AMD Dynamic Function eXchange (DFX), connecting a VexRiscv soft-core to a reconfigurable Custom Function Unit (CFU) partition. A DFX Decoupler isolates the CFU interface during reconfiguration, while a hardware counter driven by STARTUPE2 measures swap latency at 10 ns resolution.

## Project Scope

- Integrate CFU Playground, VexRiscv, and AMD DFX into one runtime-reconfigurable system.
- Create a reconfigurable `cfu_compute` partition connected through the CFU handshake interface.
- Use a DFX Decoupler to clamp CFU signals while partial bitstreams are loaded.
- Measure reconfiguration latency from the trigger event to End-of-Startup.
- Validate module swaps on Arty A7-100T and PYNQ-Z2 boards.
- Study recent DPR toolflows and document the practical barriers to reproducing them.

## Architecture

The system is split into a static region and a reconfigurable partition.

The static region contains the LiteX/VexRiscv SoC, ICAP or PCAP control path, DFX Decoupler, button trigger logic, and the reconfiguration latency counter. The reconfigurable partition contains the CFU implementation exposed to the RISC-V processor as custom instructions.

During a swap, the decoupler holds the CFU interface in a safe state, the partial bitstream is loaded, STARTUPE2 reports startup completion, and the decoupler releases the new module for use by firmware.

## Reconfigurable Modules

| Module | Purpose |
|--------|---------|
| `example.v` | Minimal add/pass-through module used as a sanity check. |
| `donut.v` | Fixed-point MAC-style accelerator for ML-oriented experiments. |
| `dse_template.v` | Parameterized design-space exploration template. |
| `kws` | Keyword-spotting accelerator prototype. |

## Results

The team successfully swapped CFU modules at runtime and executed custom RISC-V instructions immediately after each swap.

| Metric | Result |
|--------|--------|
| Arty A7-100T | JTAG-based reconfiguration working |
| PYNQ-Z2 | JTAG and PCAP reconfiguration working |
| Mean JTAG swap latency | About 28.4 ms |
| Measured latency range | About 27.9 ms to 29.1 ms |
| Projected SPI flash fetch latency | About 10 ms |

Functional checks included `custom-0(5, 3) -> 8` for the simple module and a MAC result matching the software reference for `donut.v`. The VexRiscv UART remained active through swaps, confirming that the static partition continued running.

## Toolflow Study

Alongside the implementation, the project included a study of three recent DPR toolflows:

- RapidStream, for parallel physical implementation of HLS dataflow designs.
- HPR, for page-based hierarchical partial reconfiguration.
- ZyPR, for Zynq-based runtime partial reconfiguration.

The reproduction attempts exposed practical barriers: revoked or unavailable API access, Vivado version conflicts, kernel driver issues, memory pressure during implementation, and licensing restrictions.

## Next Steps

- Fetch partial bitstreams autonomously from QSPI flash instead of relying on host-driven JTAG.
- Compare ICAP and PCAP reconfiguration latency using identical bitstream sizes.
- Add multiple reconfigurable partitions for concurrent accelerator modules.
- Build a firmware-level scheduler that preloads CFUs based on runtime usage.
