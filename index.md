# HAKC + MSV

<img src="assets/hakc-logo.png" alt="HAKC + MSV Logo" width="400">

Hardware-Assisted Kernel Compartmentalization and
Memory Safety Verification

HAKC is a compiler and operating-system research project
investigating hardware-assisted enforcement of fine-grained
memory-access policies in the Linux kernel.

MSV provides static analysis capable of proving when memory
accesses are safe, allowing HAKC instrumentation to be removed
where runtime enforcement is unnecessary.

HAKC assigns every symbol (e.g., function and global variable) in
the kernel to belong to exactly one compartment.
For symbols that have a non-zero compartment ID (we reserve compartment
0 for trusted kernel code that does not perform validation), the
compiler adds instrumentation that validates all pointers are accessible
by the compartment.
The validation involves computing a hash of the pointer value, the
compartment ID, and a memory tag associated with the pointer, and comparing
the hash with the value stored in the upper bits of the pointer.

By default, HAKC checks every pointer in compartmentalized code.
However, some pointers are only ever accessed safely and thus do not
need to be validated. In those cases, the pointer can simply be used,
which improves performance because the pointers do not require compartment
transfers or validation checks. MSV performs the analysis to identify safe
pointers that can be removed from HAKC tracking.

## Projects

* [HAKC](https://github.com/HAKC-MSV/HAKC)
* [HAKC LLVM](https://github.com/HAKC-MSV/llvm-project)
* [HAKC Linux](https://github.com/HAKC-MSV/linux)
* [MSV](https://github.com/HAKC-MSV/MSV)

## Getting Started

See the [README](https://github.com/HAKC-MSV/HAKC) in the high-level HAKC repository.

## Publications

See [Publications](publications.md).

