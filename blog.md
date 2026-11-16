# ORCA Celebrates One Year of Advancing Software Compartmentalization

*November 2026 | Open Robust Compartmentalization Alliance (ORCA)*

This November marks the **one-year anniversary of the Open Robust Compartmentalization Alliance (ORCA)**, a Linux Foundation initiative dedicated to advancing practical software compartmentalization. Since its launch in November 2025, ORCA has brought together researchers, developers, and industry partners working toward a common goal: making software systems more secure and resilient by limiting the impact of vulnerabilities through strong isolation boundaries.

Traditional software security approaches often focus on preventing vulnerabilities from occurring or being exploited. Compartmentalization provides an important complementary defense by limiting the damage that an attacker can cause after successfully exploiting a vulnerability. By dividing software into isolated components and enforcing least-privilege access between them, compartmentalization can prevent a single compromised component from compromising an entire application or system.

ORCA provides an open, vendor-neutral community for developing, evaluating, and deploying these technologies across the software stack, from hardware and operating systems to language runtimes and applications.

## Welcoming Our First Sandbox Projects

To celebrate its first anniversary, ORCA is pleased to announce the **acceptance of its first two Sandbox projects: Lind and HAKC+MSV**.

These projects represent complementary approaches to software compartmentalization, demonstrating how isolation technologies can protect both user-space applications and operating system kernels.

### Lind: Lightweight Application Compartmentalization

**[Lind](https://github.com/Lind-Project/lind-wasm)** is an open-source framework for securely executing multiple untrusted POSIX applications as isolated compartments within a single unprivileged host process. Lind aims to make strong application isolation practical without requiring changes to the operating system kernel or elevated privileges.

Lind introduces the concept of *cages*, lightweight compartments that provide each application with its own isolated memory, control flow, and POSIX state. Unlike traditional process-based isolation, Lind enables multiple mutually untrusted applications to execute within a single host process while maintaining security boundaries between them.

A distinguishing feature of Lind is its separation of the underlying isolation mechanism from the system-call interface. Lind's **3i (interposition) layer** routes application system calls through a programmable interface, allowing calls to be handled by a trusted POSIX runtime called *RawPOSIX* or by customizable user-space policy handlers known as *grates*. This architecture enables fine-grained control over application behavior, including restricting filesystem access, network operations, and other system resources.

Lind currently uses **WebAssembly-based software fault isolation through Wasmtime**, with ongoing development exploring hardware-assisted isolation mechanisms such as Intel Memory Protection Keys (MPK). Its modular architecture allows isolation backends to be replaced without modifying applications or the system-call mediation layer.

By supporting POSIX applications written in languages such as C, C++, and Rust with recompilation but generally without source-code changes, Lind seeks to lower the barriers to adopting compartmentalization. The project already supports complex workloads, including a full LAMP stack, language interpreters, and compilers.

Lind's acceptance into ORCA brings an innovative approach to application-level compartmentalization into the alliance and provides opportunities for collaboration on isolation mechanisms, security policies, and practical deployment.

Learn more through the [Lind project repository](https://github.com/Lind-Project/lind-wasm) and its [ORCA Sandbox application](https://github.com/ORCA-LF/tsc/issues/6).

### HAKC+MSV: Hardware-Assisted Linux Kernel Compartmentalization

**Hardware-Assisted Kernel Compartmentalization with Memory Safety Validation (HAKC+MSV)** is a Linux kernel compartmentalization project developed by researchers at MIT Lincoln Laboratory and collaborators.

HAKC uses hardware security mechanisms to isolate kernel components, enforcing access restrictions between compartments to limit the impact of vulnerabilities in privileged kernel code. The original HAKC research received the **Distinguished Paper Award at NDSS 2022**.

HAKC+MSV extends this approach by incorporating memory safety analysis to identify accesses that can be proven safe, reducing the runtime instrumentation and performance overhead associated with enforcing compartment boundaries.

The project aims to make fine-grained kernel compartmentalization practical for real-world Linux deployments, with a long-term goal of integrating compartmentalization capabilities into the mainline Linux kernel.

Learn more through the [HAKC+MSV GitHub organization](https://github.com/HAKC-MSV).

Together, Lind and HAKC+MSV represent an important milestone for ORCA as it transitions from establishing its community and governance to supporting concrete open-source technologies.

## ORCA Is Accepting New Projects

**ORCA continues to welcome applications from projects interested in joining the alliance.**

Projects can initially join at the **Sandbox** level, which provides an entry point for promising research efforts, emerging technologies, and early-stage open-source projects. As projects mature, they may advance through ORCA's Incubating and Graduated stages.

ORCA welcomes approaches spanning multiple layers of the computing stack, including operating system and kernel isolation, application sandboxing, hardware-assisted security, virtualization, capability-based systems, and programming language techniques.

Accepted projects benefit from collaboration with the broader compartmentalization community, technical guidance from ORCA's Technical Steering Committee, and access to Linux Foundation resources and infrastructure.

Researchers, developers, and organizations interested in joining are encouraged to review the [ORCA Call for Projects](https://orca-lf.org/call-for-projects.html) and submit an application.

Questions about participation can also be directed to [info@orca-lf.org](mailto:info@orca-lf.org).

## Recent Research in Compartmentalization

Research into software compartmentalization continues to advance rapidly, with new approaches addressing the security, performance, scalability, and usability challenges associated with deploying isolation mechanisms in real systems.

The following papers, published at leading security conferences, highlight recent developments across kernel compartmentalization, application isolation, and hardware-assisted security.

- **[kSFS: Repurposing a Microkernel-like Interface for Fast and Secure In-Kernel Linux File Systems](https://www.usenix.org/system/files/conference/usenixsecurity26/sec26_prepub_peng-dinglan.pdf)** — *USENIX Security 2026*. Introduces an approach to sandboxing Linux file systems within the kernel, using a FUSE-inspired interface and WebAssembly-based isolation to provide strong security boundaries while maintaining practical performance.

- **[BULKHEAD: Secure, Scalable, and Efficient Kernel Compartmentalization with PKS](https://www.ndss-symposium.org/ndss-paper/bulkhead-secure-scalable-and-efficient-kernel-compartmentalization-with-pks/)** — *NDSS 2025*. Presents a Linux kernel compartmentalization architecture using Intel Protection Keys for Supervisor (PKS) to provide scalable isolation between mutually untrusted kernel components.

- **[EKC: A Portable and Extensible Kernel Compartment for De-Privileging Commodity OS](https://www.usenix.org/conference/usenixsecurity25/presentation/yan-jiaqin)** — *USENIX Security 2025*. Proposes an isolated kernel compartment architecture intended to reduce the trusted computing base of commodity operating systems while supporting portability across processor architectures.

- **[Secure Caches for Compartmentalized Software](https://www.usenix.org/conference/usenixsecurity25/presentation/arikan)** — *USENIX Security 2025*. Investigates cache side-channel vulnerabilities in compartmentalized systems and proposes hardware cache partitioning techniques to protect sensitive information across compartment boundaries.

- **[Dorami: Privilege Separating Security Monitor on RISC-V TEEs](https://www.usenix.org/conference/usenixsecurity25/presentation/kuhne)** — *USENIX Security 2025*. Explores privilege separation within RISC-V trusted execution environments, isolating security monitors from other privileged firmware to reduce the attack surface.

- **[Endokernel: A Thread Safe Monitor for Lightweight Subprocess Isolation](https://www.usenix.org/conference/usenixsecurity24/presentation/yang-fangfei)** — *USENIX Security 2024*. Introduces an in-process security monitor that supports lightweight subprocess isolation while addressing multithreading and operating system interfaces that can undermine compartment boundaries.

These publications demonstrate the continued momentum behind compartmentalization research and the importance of collaboration between academia, industry, and open-source communities to translate research advances into widely deployable technologies.

## Join the ORCA Community

As ORCA enters its second year, the alliance looks forward to expanding its portfolio of open-source projects, strengthening collaboration across the compartmentalization research community, and accelerating the adoption of practical isolation technologies.

Whether you are developing a new compartmentalization mechanism, maintaining an existing open-source security project, or interested in deploying compartmentalization in real-world systems, ORCA welcomes your participation.

**Help us build a future where a single software vulnerability does not have to become a system-wide compromise.**

Learn more at **[orca-lf.org](https://orca-lf.org/)** and explore opportunities to [bring your project to ORCA](https://orca-lf.org/call-for-projects.html).
