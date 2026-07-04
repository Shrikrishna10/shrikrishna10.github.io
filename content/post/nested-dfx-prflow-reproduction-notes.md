---
title: "Nested DFX / PRFlow Reproduction Notes"
date: 2025-06-24T00:00:00Z
draft: false
summary: "Reproduction and adaptation notes for the FPT2022 Nested DFX / PRFlow framework on ZCU102 with Vitis 2021.1."
---

These are reproduction and adaptation notes for an existing research framework, not a standalone project claim. The source repository is based on the FPT2022 Nested DFX / PRFlow work on fast FPGA development using hierarchical partial reconfiguration.

The upstream framework extends earlier PRFlow and PLD work by generating variable-sized partial-reconfiguration pages through Nested DFX, then assigning operators to single, double, or quad pages based on resource needs.

## Upstream Context

The framework targets hierarchical partial reconfiguration on the Xilinx ZCU102 platform. Its main idea is to build an overlay where smaller PR pages can be recombined into larger pages, giving the toolflow more flexibility than fixed-size page layouts.

The README also notes that the FPT2022 artifact received available, functional, reusable, and replicated artifact badges. That matters for these notes because the local work is about reproducing and adapting a published framework, not presenting the framework as original work.

## Setup Target

The documented environment is Ubuntu 20.04 with Vitis 2021.1 and a ZCU102 evaluation board. The setup requires:

- Xilinx Vitis 2021.1 configuration paths.
- A ZynqMP common image from the Vitis embedded platform release.
- A generated ZCU102 Base DFX platform.
- A modified static implementation constraint file to reserve more area for the dynamic region.

Those version and platform constraints are strict enough that reproducing the flow on a different host setup is part of the work, not a trivial rerun.

## Overlay Generation

The overlay flow subdivides the base dynamic region into hierarchical PR pages, runs place-and-route after each subdivision step, and finally generates intermediate bitstreams and abstract shells from the same final routed design.

That same-design requirement is important: partial bitstreams generated from the abstract shells need to be mutually compatible with the page hierarchy and routing context.

## Expected Rough Edges

The upstream notes call out practical issues in the Vivado flow, including a partial-antenna DRC error during overlay generation. The workaround involves opening Vivado, manually running specific Tcl scripts, then continuing the Makefile and report-generation steps.

The flow is also compute-heavy. Overlay generation can take several hours because the Nested DFX steps are sequentialized by the tool behavior.

## Compile and Reporting Flow

After overlay generation, operator compilation runs HLS and Vivado synthesis jobs, synchronizes after synthesis, assigns operators to suitable PR page sizes, and launches implementation to generate partial bitstreams.

The reporting flow parses logs to summarize compile time for HLS, synthesis, and implementation. The local reproduction notes track this as a toolflow study: environment setup, overlay generation, compile-time reporting, and where manual intervention is expected.

## Local Status

The local copy is still in reproduction/adaptation state. The main remaining cleanup is making file paths, platform paths, and host-specific configuration consistent enough for a final end-to-end run. Until that is done, it should be treated as framework reproduction notes rather than a completed local implementation.
