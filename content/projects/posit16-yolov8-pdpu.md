+++
title = "Hybrid Posit16 PDPU Array for YOLOv8 Acceleration"
date = 2026-03-13T00:00:00Z
draft = false
summary = "Team in-progress accelerator RTL package for YOLOv8-style compute using Posit16 arithmetic, banked PDPUs, SiLU LUTs, converters, and golden-vector tooling."
+++

This is a team, in-progress RTL package for exploring Posit16 arithmetic in a YOLOv8-style accelerator datapath. The current focus is the PDPU compute block: Posit16 multiply/accumulate processing elements, a banked array structure, reduction logic, activation lookup, and Python tooling for golden vectors.

The project is not yet a validated accelerator implementation. The source notes explicitly keep Vivado synthesis, timing, resource checks, and full-scale validation as pending work.

## Datapath

The package centers on a hybrid DSP-assisted Posit16 multiplier. The Posit decode and encode structure is preserved from the earlier multiplier path, while the fraction multiply is moved to an exact DSP-inferred multiply instead of the older Mitchell-style approximation.

Each PDPU processing element uses the multiplier output in an accumulation path. The design keeps accumulation in regular FPGA carry-chain logic rather than trying to force the whole operation into DSP accumulator mode.

## Array Structure

The newer array is generated as multiple banks of multiple processing elements. In the current configuration notes, `N_BANKS = 8` and `N_PES = 16` are used as the intended full-scale direction, with smaller local testbench settings for quick simulation.

The banked structure allows several output channels to be computed in parallel while sharing an activation stream and loading separate weight sets per bank.

## Supporting Blocks

The RTL package includes:

- `hybrid_posit_mult.sv` for DSP-assisted Posit16 multiplication.
- `pdpu_pe.sv` for the processing element.
- `pdpu_reduction_tree.sv` with a parameterized binary adder tree.
- `pdpu_array_banked.sv` for the generated multi-bank array.
- `silu_lut.sv` and `silu_lut.hex` for a 256-entry SiLU activation lookup table.
- `posit16_int16_convert.sv` for Posit16 and INT16 boundary conversion.

## Tooling

Python scripts in the project generate golden vectors, multiplication vectors, SiLU LUT contents, and weight conversion artifacts. The notes also track script cleanup work, including dependency issues around Posit16 helper code and optional numerical packages.

That tooling is important because the accelerator needs a reproducible reference path before the RTL can be trusted at larger array sizes.

## Current Status

The RTL package includes testbenches for the multiplier, processing element, reduction tree, and banked array. Remaining work includes running Vivado synthesis for 16-PE and 128-PE configurations, checking utilization and timing, validating against golden vectors, adding debug probes for system integration, and tightening documentation around the final YOLOv8 mapping.
