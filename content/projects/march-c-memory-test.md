+++
title = "March C- Memory Fault Test Experiment"
date = 2025-11-01T00:00:00Z
draft = false
summary = "Compact SystemVerilog memory-testing experiment with a 64x64 bit model, injected faults, and a March C- style write/read sweep testbench."
+++

This is a compact SystemVerilog experiment around memory fault modeling and March-style testing. The design models a 64x64 single-bit memory array and deliberately injects several fault classes so a testbench can sweep the address space and report mismatches.

The goal is not to present a complete production MBIST engine. It is a small verification note that makes common memory fault ideas visible in RTL and simulation.

## Memory Model

The memory is parameterized with 6-bit row and column addresses, giving 64 rows by 64 columns. Each cell stores one bit. The model supports synchronous reset, single-bit writes, and synchronous reads through row and column address inputs.

Because the array is small and bit-addressed, it is easy to inspect which injected faults should appear during the test sequence.

## Injected Faults

The RTL introduces multiple fault behaviors inside the memory model:

| Fault type | Example in the model |
|------------|----------------------|
| Stuck-at fault | Selected cells forced to `0` or `1` |
| Coupling fault | One cell affected when another cell is set |
| Transition fault | A selected cell toggles unexpectedly |
| Neighborhood pattern-sensitive fault | A victim cell changes when neighboring cells match a pattern |

These injected behaviors make the testbench produce concrete failure messages rather than only proving the sweep can run.

## Testbench Flow

The testbench uses a March C- style sequence:

- Reset the memory.
- Write `0` to every cell in ascending row and column order.
- Read `0` from every cell, then write `1`.
- Read `1` from every cell in descending row and column order.

On each read, the testbench compares the observed bit with the expected value and prints the row, column, phase label, actual value, and expected value when a mismatch is found.

## What It Shows

The experiment is useful as a small bridge between memory-test theory and executable RTL. It shows how stuck-at, coupling, transition, and pattern-sensitive faults can be represented in a model, and how a deterministic March-style sweep can expose them through self-checking simulation output.

The natural next step would be turning the procedural testbench into a reusable MBIST controller with explicit address generation, data generation, comparison, and fault logging hardware.
