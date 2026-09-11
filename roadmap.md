# HAKC Project Roadmap

**Current Version**: Beta (LLVM-based)  
**Target Release**: v1.0 (Transpiler-based) - January 2027 (6 months)

---

## Executive Summary

### Vision

Enable kernel-level compartmentalization without requiring custom compiler builds, making HAKC accessible to enterprise Linux distributions and suitable for upstream kernel inclusion.

### Strategic Goal

Achieve adoption by enterprise Linux distributions (RedHat, SUSE, Canonical) by transitioning from a custom LLVM compiler pass to a **compiler-agnostic C-to-C transpiler architecture**. This architectural shift eliminates the primary barrier to upstream adoption while maintaining the security guarantees and performance characteristics of the current system.

### Current Phase

**Beta → Transpiler Architecture Transition → v1.0 Production Release**

We are pivoting from an LLVM IR-based transformation pass to a source-to-source transpiler that:
- Uses **Clang AST** for parsing and type information extraction
- Generates **modified C source code** that can be compiled with any standard compiler (GCC, Clang, ICC)
- Preserves the existing analysis infrastructure (Kuzu database, NetworkX policy generation)
- Integrates seamlessly with the kernel build system (Kbuild)

### Timeline

**6-month initial release target** with 4 major phases:
1. **Foundation** (Months 1-2): Architecture design + prototype
2. **Core Implementation** (Months 3-4): Feature-complete transpiler
3. **Validation & Hardening** (Month 5): Performance + compatibility testing
4. **Release Preparation** (Month 6): Documentation + upstream engagement

---

## Current Status

**Version**: Beta (LLVM-based)  
**As of**: July 2026

### What Works Today

- ✅ **Full x86_64 compartmentalization** with analysis + enforcement pipeline
- ✅ **ARM64 support** with hardware features (MTE memory tagging + PAC pointer authentication)
- ✅ **ROS2 demonstration** running in QEMU with compartment isolation
- ✅ **Graph-based policy analysis** using Kuzu database + NetworkX algorithms
- ✅ **Runtime kernel support**:
  - Pointer signing and verification
  - Memory tagging (16-color scheme)
  - Transfer functions for cross-compartment calls
  - Per-CPU variable handling
- ✅ **Multiple compartmentalization algorithms** (Greedy, Filesystem-based, Size-balanced)
- ✅ **CVE-based vulnerability analysis** for demonstrating security benefits
- ✅ **Comprehensive test suite** (LLVM pass tests + kernel runtime tests)

### Known Limitations

- ⚠️ **Requires custom LLVM build** - blocks distribution adoption
- ⚠️ **Version-locked to specific LLVM release** - difficult to track upstream
- ⚠️ **IR-level transformations** - complicates debugging and code inspection
- ⚠️ **Long build times** - rebuilding LLVM for development iteration is slow
- ⚠️ **Limited to Clang/LLVM toolchain** - cannot use GCC or vendor compilers

These limitations prevent upstream kernel adoption and distribution acceptance, motivating the transpiler transition.

---

## Architecture Transition: LLVM → Transpiler

### Problem Statement

Enterprise Linux distributions require using their **validated compiler toolchains** and will not adopt solutions requiring custom compiler builds. Key concerns:

1. **Maintenance burden**: Tracking LLVM upstream changes and maintaining patches
2. **Security validation**: Distros must re-certify any modified compiler
3. **Toolchain flexibility**: Need to support GCC (primary kernel compiler) and vendor toolchains
4. **Debugging complexity**: IR-level changes obscure source code debugging
5. **Build infrastructure**: Existing kernel build systems expect standard compilers

### Solution: C-to-C Transpiler Architecture

**Three-stage build process**:

```
┌─────────────────────────────────────────────────────────────────┐
│ Stage 1: Analysis                                               │
│ ┌─────────────┐    ┌──────────────┐    ┌─────────────────────┐ │
│ │ Clang AST   │───▶│ Type/Symbol  │───▶│ Kuzu Graph Database │ │
│ │ Parser      │    │ Extractor    │    │ (DAG + Edges)       │ │
│ └─────────────┘    └──────────────┘    └─────────────────────┘ │
│                                                     │            │
│                                         ┌───────────▼─────────┐ │
│                                         │ Policy Generator    │ │
│                                         │ (NetworkX Analysis) │ │
│                                         └───────────┬─────────┘ │
│                                                     │            │
│                                         ┌───────────▼─────────┐ │
│                                         │ Compartment Policy  │ │
│                                         └─────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│ Stage 2: Transpilation                                          │
│ ┌─────────────┐    ┌──────────────┐    ┌─────────────────────┐ │
│ │ Original    │───▶│ C-to-C       │───▶│ Transformed         │ │
│ │ Kernel      │    │ Transpiler   │    │ Kernel Source       │ │
│ │ Source      │    │ (AST-based)  │    │ (with HAKC)         │ │
│ └─────────────┘    └──────────────┘    └─────────────────────┘ │
│                            ▲                                    │
│                            │                                    │
│                    ┌───────┴────────┐                          │
│                    │ Policy Server  │                          │
│                    │ (queries DB)   │                          │
│                    └────────────────┘                          │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│ Stage 3: Compilation                                            │
│ ┌─────────────┐    ┌──────────────┐    ┌─────────────────────┐ │
│ │ Transformed │───▶│ Standard     │───▶│ HAKC-Protected      │ │
│ │ Source      │    │ Compiler     │    │ Kernel Binary       │ │
│ │             │    │ (GCC/Clang)  │    │                     │ │
│ └─────────────┘    └──────────────┘    └─────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

### Key Technical Decisions

| Component | Current (LLVM) | New (Transpiler) | Rationale |
|-----------|---------------|------------------|-----------|
| **Input Format** | C source → LLVM IR | C source → Clang AST | AST preserves source structure |
| **Type Information** | DWARF debug info | Clang AST traversal | Direct access to type system |
| **Transformation** | IR instruction manipulation | Source code generation | Any compiler can build output |
| **Output Format** | Modified IR → Object files | Modified C source | Human-readable, debuggable |
| **Compiler Support** | Clang only | GCC, Clang, ICC, etc. | Distro flexibility |
| **Build Integration** | LLVM pass via `-fpass-plugin` | Kbuild preprocessing step | Standard kernel workflow |

### Key Benefits

- ✅ **Compiler independence**: Works with any C compiler (GCC, Clang, vendor toolchains)
- ✅ **Faster development**: No LLVM rebuild required for iteration
- ✅ **LLVM version independence**: Only need Clang libtooling for analysis, not full build
- ✅ **Better debuggability**: Source-level transformations preserve line numbers and structure
- ✅ **Upstream acceptance**: Acceptable for mainline kernel (similar to pahole, objtool)
- ✅ **Transparent operation**: Generated code is human-readable and inspectable

### Performance Target

**< 5% runtime overhead** compared to baseline kernel (same as current LLVM approach)

## Beyond v1.0: Future Work

### Research & Experimental (Post-v1.0)

**Advanced Compartmentalization Policies**
- [ ] **Machine learning-based policy generation**
  - Learn optimal compartment boundaries from runtime traces
  - Minimize performance overhead while maximizing security
  - Adapt to workload-specific patterns
- [ ] **CVE-aware compartmentalization**
  - Automatically analyze CVE patterns to inform compartment boundaries
  - Prioritize isolating historically vulnerable subsystems
  - Dynamic policy updates based on new CVE disclosures
- [ ] **Dynamic policy adjustment**
  - Runtime policy reconfiguration without reboot
  - Adaptive compartmentalization based on threat level
  - Hot-patching of compartment boundaries

**Extended Platform Support**
- [ ] **RISC-V architecture**
  - RISC-V pointer masking extension (Zpm)
  - RISC-V capability-based protection (CHERI-RISC-V)
- [ ] **Additional ARM variants**
  - ARMv8.5-A MTE refinements
  - ARMv9 enhancements
- [ ] **x86_64 hardware features**
  - Intel LAM (Linear Address Masking)
  - Intel CET (Control-flow Enforcement Technology)
  - AMD memory tagging extensions

**Upstream Integration**
- [ ] **Mainline kernel inclusion**
  - Merge HAKC runtime into mainline kernel
  - Upstream Kconfig option: `CONFIG_HAKC`
  - Long-term maintenance and stability
- [ ] **Distribution adoption tracking**
  - Monitor which distros adopt HAKC
  - Gather feedback from distro maintainers
  - Support distro-specific requirements
- [ ] **Production deployment case studies**
  - Document real-world deployments
  - Performance data from production systems
  - Security incident reports (prevented attacks)

### Long-term Vision (3-5 years)

- **Standard kernel hardening mechanism** accepted by upstream Linux kernel
- **Adopted by major distributions** (Fedora, RHEL, Ubuntu, SUSE) by default
- **Extended to userspace compartmentalization** for system services and applications
- **Industry standard** for privilege separation in operating systems
- **Academic adoption** as a platform for compartmentalization research

---

## Non-Goals (v1.0 Scope)

Explicitly **out of scope** for v1.0 to manage expectations and maintain focus:

- ❌ **Full GCC plugin** - We require Clang libtooling for AST parsing during analysis phase, but GCC (or any compiler) can build the transpiler output. This is acceptable for distros.
- ❌ **Userspace application compartmentalization** - v1.0 is kernel-only. Userspace compartmentalization is a future research direction.
- ❌ **Automatic CVE remediation** - Policy generation is manual or semi-automated. Fully automatic CVE-aware policies are research work.
- ❌ **Real-time kernel support** (`PREEMPT_RT`) - Focus on general-purpose kernels first. RT kernel support requires additional latency analysis.
- ❌ **Android kernel integration** - Mainline Linux only. Android has different build system and security model.
- ❌ **Windows/macOS support** - HAKC is Linux-specific, leveraging kernel-specific features.
- ❌ **Microkernel architectures** - Currently targeting monolithic Linux kernel. Microkernel compartmentalization is a different problem.
- ❌ **LLVM pass maintenance** - Once transpiler is production-ready, the LLVM pass will be deprecated and no longer maintained (though source will remain for reference).

---

## Success Metrics

### Technical Metrics (Quantifiable Goals)

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| **Runtime Performance** | < 5% overhead vs vanilla kernel | LMBench, UnixBench, kernel selftests |
| **Build Time** | < 10% increase vs vanilla kernel build | Time `make -j$(nproc)` on reference hardware |
| **Compiler Compatibility** | 100% pass rate with GCC 11-13, Clang 15-17 | CI matrix builds, all tests pass |
| **Test Coverage** | > 80% code coverage in transpiler | gcov/lcov on transpiler codebase |
| **Boot Success** | 100% boot rate on x86_64, ARM64 | Automated QEMU tests, daily runs |
| **Functional Equivalence** | 100% of LLVM pass tests pass with transpiler | Port existing test suite, no regressions |

### Adoption Metrics (Post-v1.0, Tracked Over Time)

| Metric | 6 Months Post-Release | 12 Months Post-Release |
|--------|----------------------|------------------------|
| **Upstream RFC Status** | Initial submission, review in progress | Accepted or in final revision rounds |
| **Distribution Pilot Programs** | 1+ distro pilot (target: RedHat) | 2+ distros in pilot or production |
| **Community Contributions** | 5+ external contributors | 15+ external contributors |
| **GitHub Stars** | 100+ stars | 500+ stars |
| **Conference Presentations** | 1+ accepted talk | 3+ talks or papers |
| **Production Deployments** | 1+ production deployment | 5+ production deployments |
| **CVE Mitigations** | Document 5+ historical CVEs mitigated | Document 10+ CVEs + real-world incident |

### Community Engagement Indicators

- **Issue tracker activity**: 10+ issues filed, 80%+ resolved within 2 weeks
- **Mailing list/discussion forum**: Active discussions, responsive maintainers
- **Documentation quality**: No major gaps reported, < 5% of issues are "how do I...?"
- **Academic citations**: 3+ academic papers cite HAKC within 1 year

---

## Dependencies & Risks

### Technical Dependencies

| Dependency | Status | Risk Level | Notes |
|------------|--------|-----------|-------|
| **Clang libtooling** (AST parsing) | ✅ Stable, well-supported | Low | Mature API, widely used in Clang-Tidy, Clang-Format |
| **Kuzu graph database** | ✅ Current analysis infrastructure | Low | Already in use, stable API |
| **Kernel build system (Kbuild)** | ✅ Stable API | Low | Well-documented, stable across kernel versions |
| **ARM MTE/PAC hardware** | ⚠️ Limited availability | Medium | Emulated in QEMU, ARMv9 hardware slowly rolling out |
| **Python 3.9+** | ✅ Stable | Low | Required for analysis scripts |
| **NetworkX** | ✅ Stable | Low | Graph analysis library |

### Risks & Mitigations

| Risk | Impact | Likelihood | Mitigation |
|------|--------|-----------|------------|
| **Transpiler performance doesn't meet target** | High | Medium | Early prototyping in Phase 1, continuous benchmarking, iterative optimization |
| **GCC compatibility issues with kernel code** | High | Medium | Test against multiple kernel versions (5.15 LTS, 6.1 LTS, 6.6 LTS), close engagement with kernel team |
| **Type information incomplete from AST** | Medium | Low | Fall back to BTF (BPF Type Format) if needed, validate early in Phase 1 |
| **Upstream resistance to complexity** | High | Medium | Demonstrate value with CVE case studies, minimize kernel changes, show distro demand |
| **Timeline slippage** | Medium | Medium | Prioritize ruthlessly (P0 only for v1.0), defer non-critical features to v1.1 |
| **Transpiler bugs introduce security vulnerabilities** | High | Low | Extensive testing, fuzzing, security review, formal verification (future work) |
| **RedHat/distro requirements change** | Medium | Low | Maintain close communication with distro stakeholders, adapt quickly |
| **Compiler behavior differences cause functional bugs** | Medium | Medium | Comprehensive cross-compiler testing, CI matrix, conservative code generation |

---

## Questions or Feedback?

- **Email**: hakc@ll.mit.edu
- **Issue tracker**: https://github.com/HAKC-MSV/HAKC/issues
