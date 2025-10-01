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

let’s set up **Grype in GitLab CI/CD** so every pipeline run automatically scans your Docker images or filesystem for vulnerabilities.

---

# 🔹 Steps to Integrate Grype in GitLab CI/CD

### 1. Install Grype in the Pipeline

You can either:

* Use the **official Anchore Grype image**, or
* Download & install the binary inside your job.

---

### 2. Example `.gitlab-ci.yml`

Here’s a simple GitLab CI configuration to scan a **Docker image** after it’s built:

```yaml
stages:
  - build
  - security_scan

# Example build stage (build your app image)
build:
  stage: build
  image: docker:20.10
  services:
    - docker:20.10-dind
  script:
    - docker build -t myapp:latest .

# Security scan using Grype
grype_scan:
  stage: security_scan
  image: anchore/grype:latest
  script:
    # Scan the image we just built
    - grype myapp:latest --fail-on high --output table
  allow_failure: false
```

---

### 3. Explanation

* **`anchore/grype:latest`** → official Grype Docker image.
* **`grype myapp:latest`** → scans your locally built Docker image.
* **`--fail-on high`** → pipeline will fail if vulnerabilities of **High severity** (or above) are found.
* **`allow_failure: false`** → ensures build fails if vulnerabilities exist (you can set `true` if you just want reports, not blocking).
* **`--output table`** → human-readable. You can also use `json` or `cyclonedx` if you want structured output for reports.

---

### 4. Example for Scanning Filesystem Instead of Image

If you don’t build Docker images but want to scan source code dependencies:

```yaml
grype_scan:
  stage: security_scan
  image: anchore/grype:latest
  script:
    - grype dir:/builds/$CI_PROJECT_PATH --fail-on critical --output table
```

---

### 5. Advanced: Store Reports as GitLab Artifacts

You can generate JSON output and save it:

```yaml
grype_scan:
  stage: security_scan
  image: anchore/grype:latest
  script:
    - grype myapp:latest -o json > grype-report.json
  artifacts:
    when: always
    paths:
      - grype-report.json
    reports:
      sast: grype-report.json
```

This way, GitLab will display results inside the **Security Dashboard**. 🔐

---

