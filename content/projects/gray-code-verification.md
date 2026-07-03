+++
title = "Gray Code Counter Verification Environment"
date = 2024-12-01T00:00:00Z
draft = false
summary = "SystemVerilog verification project for a 3-bit Gray code counter using generator, driver, monitor, scoreboard, and simulation logs."
+++

This project built a small SystemVerilog verification environment around a 3-bit Gray code counter. The DUT increments a binary count and emits the Gray-coded value using the standard transform `{count[2], count[2] ^ count[1], count[1] ^ count[0]}`.

The value of the project is in the testbench structure rather than the counter itself: it turns a compact sequential design into a full verification flow with stimulus, observation, and self-checking.

## DUT

The design resets both the binary count and Gray output to zero. On each clock edge after reset, the internal count increments and the Gray output is derived from the current count bits.

Because the counter is only 3 bits, the expected sequence is easy to inspect manually:

| Count | Gray |
|-------|------|
| 0 | 000 |
| 1 | 001 |
| 2 | 011 |
| 3 | 010 |
| 4 | 110 |
| 5 | 111 |
| 6 | 101 |

## Testbench Structure

The environment is organized with familiar verification components:

- `transaction.sv` carries reset duration and observed values.
- `generator.sv` randomizes transaction fields and sends them through a mailbox.
- `driver.sv` applies reset timing and passes transaction context to the interface.
- `monitor.sv` samples reset, clock, count, and Gray output from the virtual interface.
- `scoreboard.sv` recomputes the expected Gray value and reports pass/fail.
- `environment.sv`, `test.sv`, and `top.sv` connect the flow around the DUT.

## Checking Strategy

The scoreboard compares the observed output against the expected Gray transform for the observed count. It also treats reset with zero output as a correct result. This gives a simple self-checking loop where the simulation log records both transaction state and result status.

The checked log includes reset windows and count transitions through values such as `000`, `001`, `011`, `010`, `110`, `111`, and `101`, each reported as correct.

## Result

The simulation log shows the environment generating reset durations, driving the DUT, monitoring sampled values, and reporting `Result is correct` across the observed counter sequence. The project is a compact example of building a verification harness before moving to larger protocols or constrained-random stimulus.
