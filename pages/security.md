# Security Best Practices: Local & Cloud Environments

Securing the software development lifecycle (SDLC) requires a "Shift Left" approach, ensuring security is integrated from the local workstation to the production cloud environment.

---

## 1. Local Linux Environment Security
The local machine is often the weakest link. Securing the workstation prevents credential theft and lateral movement.

### Hardening the OS
* **Disk Encryption:** Always use LUKS (Linux Unified Key Setup) to encrypt your hard drive. This protects data if the hardware is stolen.
* **SSH Hardening:** * Disable password authentication in `/etc/ssh/sshd_config` (`PasswordAuthentication no`).
    * Use Ed25519 SSH keys for better security and performance.
* **Firewall Management:** Enable `ufw` or `iptables` and only allow necessary incoming ports (usually none for a standard workstation).
    ```bash
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw enable
    ```

### DevSecOps Tools for Local Workflows
* **Secret Scanning:** Use `trufflehog` or `gitleaks` to scan your local commits for accidental API keys or passwords.
* **Pre-commit Hooks:** Set up scripts to run linting and security checks automatically before every `git commit`.

---

## 2. Cloud Environment Security (AWS/Azure/GCP)
Cloud security is a "Shared Responsibility Model." While the provider secures the hardware, you must secure the configuration.



### Identity & Access Management (IAM)
* **Principle of Least Privilege (PoLP):** Assign only the permissions required for a specific task. Use "Groups" or "Roles" instead of attaching policies directly to users.
* **MFA (Multi-Factor Authentication):** Enforce hardware-based MFA (like Yubikeys) or TOTP (authenticator apps) for all console users.
* **Just-In-Time (JIT) Access:** Avoid long-lived credentials. Use temporary security tokens via `aws sts` or Cloud Shells.

### Infrastructure as Code (IaC) Security
* **Static Analysis:** Use tools like `tfsec` or `Checkov` to scan Terraform/CloudFormation code for misconfigurations (e.g., publicly accessible S3 buckets).
* **State File Protection:** Ensure Terraform state files are stored in encrypted buckets with versioning and access logging enabled.

---

## 3. Network & Connectivity
* **VPC Security Groups:** Act as a virtual firewall. Never use `0.0.0.0/0` for SSH (Port 22). Restrict it to your specific IP.
* **Private Subnets:** Place databases and application servers in private subnets with no direct internet access. Use a NAT Gateway for outbound updates only.

---

## 4. Continuous Monitoring & Compliance
* **Audit Logs:** Enable AWS CloudTrail, Azure Activity Log, or GCP Cloud Audit Logs. 
* **Vulnerability Scanning:** Use tools like `Trivy` to scan container images for known CVEs before deploying them to a cloud registry (ECR/ACR).

> **Pro-Tip:** Security is not a one-time setup; it is a continuous process of auditing and refining.
