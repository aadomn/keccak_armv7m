# Optimized Keccak-f[1600] on ARMv7-M

This repository contains optimized assembly implementations of [Keccak-f[1600]](https://keccak.team/keccak.html) on ARMv7-M processors: ARM Cortex-M3, M4 and M7.
Because it consists of a rework of the code originally offered by the [eXtended Keccak Code Package](https://github.com/XKCP/XKCP), the implementations are based on various optimizations introduced by the Keccak designers in [the following document](https://keccak.team/files/Keccak-implementation-3.2.pdf).

It differentiates between the implementations tailored for the M3 and M4, and the one specifically fine-tuned for the M7. See [the corresponding note](https://eprint.iacr.org/2023/) for more details.

## Cortex-M3 and M4
For the M3 and M4, two implementations are available.

The first one, `keccakp1600_balanced_cortexm3-4.S`, implements in-place processing, bit-interleaving on top of lazy rotations for three-quarters of the round. This constitutes my primary recommendation as it is a good trade-off between efficiency and code size.

The second one, `keccap1600_fast_cortexm3-4.S`, is very similar but implements lazy rotations for all rounds, making it slightly faster (about 200 clock cycles, which represents a gain of ~2%) but significantly larger in terms of code size (about 3 KiB, which represents an overhead of ~50%). While there is room for improvement in terms of code size, it should affect the small performance advantage. The main purpose of this file is results reproducibility.

## Cortex-M7
For the M7, only a single implementation is available: `keccakp1600_cortexm7.S` implements bit-interleaving and use dedicated intrusction scheduling to optimize dual-issue capabilities.

According to my benchmark it only provides a 10% improvement over the ARMv7-M implementation from XKCP.
However note that because we omitted in-place processing here, the code size is half reduced (from ~5.5 KiB to ~2.9 KiB) but it consumes almost twice as much RAM (~500 bytes instead of ~250 bytes).

Although I am reasonably confident in the optimality of my implementations on the M3 and M4, you might discover some scheduling techniques to further enhance the M7 code. If so, I would be happy to hear from you :-)
