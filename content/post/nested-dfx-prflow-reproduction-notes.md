---
title: "HPR / Nested DFX Toolflow Notes"
date: 2025-06-24T00:00:00Z
draft: false
summary: "Literature and toolflow notes on Hierarchical Partial Reconfiguration, recombinable PR pages, NoC-connected operators, and a failed local reproduction attempt."
---

These are literature and toolflow notes based on my HPR notes and the FCCM 2022 Hierarchical Partial Reconfiguration paper. The work is treated as a reference study rather than a completed local reproduction, because my attempt to run the toolflow was ultimately abandoned.

The useful takeaway for my DPR capstone was the architecture idea: recombinable partial-reconfiguration pages connected by a NoC, so operators can be mapped to a page size that fits them instead of forcing every operator into one fixed-size region.

## Core Idea

HPR uses hierarchical, recombinable PR pages:

- Single pages for small operators.
- Double pages for medium operators.
- Quad pages for larger operators.

The pages are connected using a Network-on-Chip, with the notes pointing to a Hoplite BF-style variant. This lets the runtime connect operators after their partial bitstreams are loaded.

The main benefit is that the user does not have to manually decompose each operator to fit a fixed PR slot. The framework can place an operator into the smallest page size that satisfies its resource needs.

## Workflow

The HPR flow is organized around independent operator compilation:

1. Operators are written in C or C++.
2. Vitis HLS synthesizes operators in parallel and emits resource-utilization reports.
3. The tool parses those reports and greedily assigns each operator to the smallest fitting Single, Double, or Quad page.
4. Assigned pages are placed and routed in parallel.
5. Partial bitstreams are generated for each operator page.
6. At runtime, the NoC is configured to connect the operators.

This makes the flow more adaptive to operator size than fixed-page PR systems.

## Comparison

The comparison that mattered most in my notes was:

| Approach | Page model | Practical issue |
|----------|------------|-----------------|
| PR with NoC | Fixed PR pages connected by a NoC | Operators must fit fixed slots or be manually split |
| RapidStream | Fixed implementation islands | Helps compilation, but still depends on partitioning and stitching |
| HPR | Hierarchical Single/Double/Quad pages | Adapts page size to operator resource needs |

Compared with fixed-size approaches, HPR reduces manual decomposition and wasted area from mismatched page sizes. Compared with RapidStream, the emphasis is not only compile-time improvement, but runtime reconfiguration of operators through a hierarchical PR overlay.

## Reported Results

The paper reports synthesis-time reduction and lower manual effort because operators can be compiled independently and mapped to page sizes automatically. The important result for my work was conceptual: a controller does not need to assume one accelerator size or one fixed slot if the fabric can expose recombinable pages.

This directly informed the proposed CFU controller direction in the DPR project: keep the runtime interface flexible enough that different accelerator modules can be loaded and connected without baking every module into the static design.

## Implementation Note

I attempted to reproduce the HPR toolflow, but abandoned the run because of version and resource constraints. The setup ran into a combination of Vivado-version mismatch, Linux-kernel mismatch, and driver or library conflicts.

The available machine was also pushed well past its limits during place-and-route. Jobs failed or hung under the compute and memory pressure. For that reason, HPR stayed as a literature reference in my report rather than a reproduced implementation result.
