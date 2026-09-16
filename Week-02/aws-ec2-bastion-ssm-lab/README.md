## 📊 Comparison: Bastion Host vs. AWS SSM Session Manager

| Evaluation Criteria | Bastion Host (SSH Jump Box) | AWS SSM Session Manager |
| :--- | :--- | :--- |
| **Inbound Security** | Requires Port 22 open inbound to admin IPs | **Zero open inbound ports required** |
| **Network Requirements** | Public Subnet instance + Public IP address | Works natively in private subnets via NAT/Endpoints |
| **Authentication** | Manual SSH keys (`.pem`) & key rotation | Integrated with **AWS IAM** policies and Identity Providers |
| **Audit & Logging** | Manual configuration required | Centralized, automatic session logging to **S3 / CloudWatch** |
| **Maintenance** | High (server patching, SSH key management) | **Zero server overhead** (Fully managed AWS service) |

---

### Architectural Takeaways

* **Bastion Host:** Connects to a public bridge instance over port 22 (`44.193.75.94`), then tunnels into private instances (`10.0.2.10`). While functional, open port 22 exposes an attack surface and increases SSH key management overhead.
* **SSM Session Manager:** Uses the outbound `amazon-ssm-agent` polling loop over HTTPS (Port 443). Access is granted via the `EC2-SSM-Role` IAM Instance Profile containing `AmazonSSMManagedInstanceCore`.

**Recommendation:** AWS SSM Session Manager is the enterprise standard because it completely removes public exposure, eliminates SSH key handling, and enforces central IAM access policies.
