 **Grype** is a **vulnerability scanner for container images and filesystems**.

---

## 🔹 What is Grype?

* An **open-source vulnerability scanner** maintained by **Anchore**.
* Scans **container images, filesystems, and SBOMs (Software Bill of Materials)** for known vulnerabilities.
* Works with popular registries and image formats (Docker, OCI, etc.).

---

## 🔹 Key Features

* **Detects CVEs**: Finds vulnerabilities in OS packages (Debian, Alpine, RHEL, etc.) and application dependencies (Python, Java, Ruby, etc.).
* **Multiple inputs**: Can scan:

  * Docker/OCI images (`docker://` or `oci://`)
  * Filesystem directories (`dir://`)
  * SBOM files (`sbom://`)
* **Integrates with CI/CD**: Easy to add in GitHub Actions, GitLab CI, Jenkins, etc.
* **Lightweight**: Simple CLI tool.

---

## 🔹 Installation

On Linux/macOS:

```bash
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh
```

Or via Homebrew:

```bash
brew tap anchore/grype
brew install grype
```

---

## 🔹 Usage Examples

1. **Scan a local Docker image**:

   ```bash
   grype myapp:latest
   ```

2. **Scan an image in a registry**:

   ```bash
   grype docker.io/library/nginx:1.21
   ```

3. **Scan a local filesystem directory**:

   ```bash
   grype dir:/path/to/project
   ```

4. **Generate SBOM and scan**:

   ```bash
   syft myapp:latest -o json > sbom.json
   grype sbom:sbom.json
   ```

---

## 🔹 Sample Output

Grype gives a table with:

* **Vulnerability ID (CVE)**
* **Package Name**
* **Installed Version**
* **Fixed Version (if available)**
* **Severity (Low, Medium, High, Critical)**

Example:

```
NAME        INSTALLED  FIXED-IN   VULNERABILITY   SEVERITY
openssl     1.1.1f     1.1.1n     CVE-2022-0778   High
```

--
