# Security Policy

## Reporting a Vulnerability

**IMPORTANT: Do NOT report security vulnerabilities through public GitHub issues.**

If you discover a security vulnerability in HAKC, please report it privately to ensure responsible disclosure and to protect users while a fix is being developed.

### How to Report

HAKC has enabled vulnerability reporting on the Github repo.  Please use that for reporting vulnerabilities.  Look to [this page](https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/report-privately) 
for information on how to report privately.

### What to Include in Your Report

Please provide as much information as possible to help us understand and reproduce the issue:

1. **Description**: Detailed description of the vulnerability
2. **Impact**: What can an attacker do? What is at risk? What data or systems are compromised?
3. **Reproduction Steps**: Step-by-step instructions to reproduce the vulnerability
4. **Environment Details**:
   - Linux kernel version
   - Architecture (x86_64, aarch64, etc.)
   - HAKC version or git commit hash
   - LLVM version
   - Any relevant configuration details
5. **Proof of Concept**: Code, commands, or other artifacts demonstrating the issue (if available)
6. **Suggested Fix**: If you have ideas for remediation, please share them
7. **Credit**: How you would like to be credited when the vulnerability is disclosed (or if you prefer to remain anonymous)

## Scope

### In Scope

Security vulnerabilities in HAKC-specific code:
- LLVM compartmentalization passes (`llvm-project/llvm/lib/Transforms/Compartmentalization/`)
- Linux kernel HAKC integration (`linux/kernel/hakc/`, `linux/include/linux/hakc/`)
- Python policy server and analysis tools (`llvm-project/llvm/utils/hakc/`, `python/`)
- Build system vulnerabilities that could lead to exploitation
- Documentation that could lead to insecure usage

### Out of Scope

- **Upstream Vulnerabilities**: Issues in upstream LLVM or Linux kernel (report to those projects)
- **Third-Party Dependencies**: Issues in Kuzu, Python libraries, etc. (report to dependency maintainers)
- **Theoretical Issues**: Issues without practical exploit scenarios
- **Denial of Service**: In build/analysis tools only (DoS in runtime is in scope)
- **Social Engineering**: Phishing, impersonation, or other social attacks

If you're unsure whether an issue is in scope, please report it and we'll make the determination.


## Acknowledgments

We deeply appreciate security researchers and contributors who help keep HAKC secure. Responsible disclosure helps protect all HAKC users.

Security researchers who report valid vulnerabilities will be:
- Credited in security advisories (unless anonymity is requested)
- Acknowledged in project documentation
- Thanked publicly in release notes

## Contact

**For security concerns:** derrick.mckee@ll.mit.edu  
**Subject line:** `[HAKC SECURITY] <brief description>`

**For general questions:**
- Contributing: See [Contributing](contributing.md)
- Maintainers: See the HAKC or MSV MAINTAINERS.md
- Code of Conduct: See [Code of Conduct](code_of_conduct.md)

## Additional Resources

- **CVE Database**: https://cve.mitre.org/
- **CVSS Calculator**: https://www.first.org/cvss/calculator/3.1
- **Linux Kernel Security**: https://www.kernel.org/category/security.html
- **LLVM Security**: https://llvm.org/
- **Responsible Disclosure**: https://en.wikipedia.org/wiki/Responsible_disclosure

Thank you for helping keep HAKC and its users safe!
