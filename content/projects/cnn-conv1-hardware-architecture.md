+++
title = "Streaming Conv1 Hardware Architecture for LeNet-style CNNs"
date = 2025-12-07T00:00:00Z
draft = false
summary = "Architecture note for a streaming Conv1 accelerator under a 16-byte SRAM constraint, with MAC scheduling and throughput estimates."
+++

This design note explored the first convolution layer of a simplified LeNet-style CNN as a constrained hardware architecture problem. The target layer used an 8x8 grayscale input, a 3x3 kernel, stride 2, padding 1, and produced a 4x4 output feature map.

The point of the exercise was not to build a large CNN accelerator. It was to reason through how a very small scratchpad, limited MAC resources, and low external bandwidth shape the dataflow.

## Constraints

- Input pixels and weights are 8-bit signed integers.
- The design has four 8-bit by 8-bit MAC units.
- On-chip SRAM is limited to 16 bytes, modeled as dual-port storage with two 32-bit ports.
- External memory bandwidth is 1 KB/s.
- The 3x3 filter needs 9 bytes, while a full patch plus weights would need 18 bytes, so the working set cannot fit at once.
- Accumulation uses wider 20-bit registers to avoid overflow during the 3x3 dot product.

## Dataflow

The design uses a streaming, weight-aware dataflow. The 16-byte SRAM is treated as a small scratchpad rather than an image buffer: weights and activation windows are staged in batches, and pixels stream through the MAC array instead of storing the full 8x8 image locally.

Each output patch is split into three operand batches: four multiplications, four multiplications, and one final multiplication. The MAC adders are then reused in add-only mode for partial-sum reduction, avoiding extra reduction hardware.

## MAC Schedule

For each 3x3 patch, the conservative schedule in the notes accounts for SRAM loading, two-cycle multiplier latency, three-cycle adder latency, and reduction:

| Phase | Work |
|-------|------|
| Batch 0 | operands 0-3 across four MACs |
| Batch 1 | operands 4-7 across four MACs |
| Batch 2 | operand 8 on one MAC |
| Reduction | reuse MAC adders to combine partial sums |

The later timing model estimates 24 cycles per output patch. With 16 patches per image, the compute path takes 384 cycles per image.

## Throughput Estimate

The memory model counts 64 input bytes, 144 weight bytes when fetched per patch, and 48 output bytes when each 20-bit result is packed into 3 bytes. That gives 256 bytes per image.

At 1 KB/s external bandwidth, the design is bandwidth-limited to about 4 images/s. A compute clock of roughly 1.5 kHz is enough to sustain that rate under the 384-cycle schedule.

## Notes

The useful part of this project was the constraint-driven architecture work: scheduling four MAC lanes, choosing when to stream instead of buffer, and checking whether a proposed dataflow actually fits in 16 bytes. The next step would be turning the schedule into RTL with explicit control states for SRAM access, MAC mode selection, accumulator reset, and output packing.
