
# Using Veracode Reusable Workflows in Your Repository

This document explains how to use the **Veracode reusable workflows** for scanning .NET and Java applications. The reusable workflows are stored in a central repository (`security-workflows`) and can be called from any application repository using the repository name as the application name in Veracode.

---

## Prerequisites

1. **GitHub Secrets** must be configured in your repository:
   - `VERACODE_API_ID` – Your Veracode API ID.
   - `VERACODE_API_KEY` – Your Veracode API Key.

2. Ensure your application repository has the **project files** available:
   - `.sln` file for .NET projects.
   - Maven or Gradle build files for Java projects.

3. Runner environment must support building your application:
   - .NET SDK installed for .NET projects.
   - JDK installed for Java projects.

---

## 1. Calling the .NET Workflow

Create a workflow file in `.github/workflows/`, for example `veracode-dotnet-scan.yml`:

```yaml
name: Veracode Dotnet Scan

on:
  push:
    branches: [ "main" ]
  schedule:
    - cron: "0 0 */90 * *"   # Schedule scan every 90 days

jobs:
  veracode:
    uses: your-org/security-workflows/.github/workflows/veracode-dotnet.yml@main
    with:
      sln_file: "ECoupon_MAF.sln"      # Path to solution file
      project_path: "."                 # Root or subfolder of the project
    secrets:
      VERACODE_API_ID: ${{ secrets.VERACODE_API_ID }}
      VERACODE_API_KEY: ${{ secrets.VERACODE_API_KEY }}
```

### Inputs Explained

| Input Name     | Required | Description |
|----------------|----------|-------------|
| `sln_file`     | Yes      | Path to the `.sln` file in your repo |
| `project_path` | Yes      | Path to the project root (or subfolder) |

---

## 2. Calling the Java Workflow

Create a workflow file in `.github/workflows/`, for example `veracode-java-scan.yml`:

```yaml
name: Veracode Java Scan

on:
  push:
    branches: [ "main" ]
  schedule:
    - cron: "0 0 */90 * *"   # Policy scan every 90 days

jobs:
  veracode:
    uses: your-org/security-workflows/.github/workflows/veracode-java.yml@main
    with:
      build_tool: "maven"               # "maven" or "gradle"
      project_path: "."                 # Project root or subfolder
      artifact_pattern: "**/target/*.jar" # Pattern to locate build artifact
    secrets:
      VERACODE_API_ID: ${{ secrets.VERACODE_API_ID }}
      VERACODE_API_KEY: ${{ secrets.VERACODE_API_KEY }}
```

### Inputs Explained

| Input Name        | Required | Description |
|------------------|----------|-------------|
| `build_tool`      | Yes      | Build tool used: `maven` or `gradle` |
| `project_path`    | Yes      | Root or subfolder of the Java project |
| `artifact_pattern`| Yes      | Pattern to locate the build artifact (jar/war) |

---

## 3. Steps to Use Reusable Workflows

1. **Add GitHub Secrets** (`VERACODE_API_ID` and `VERACODE_API_KEY`) in your application repository.
2. **Ensure project paths are correct**:
   - For .NET, provide the relative path to the `.sln` file.
   - For Java, provide the relative path to the project root and the correct artifact pattern.
3. **Copy the workflow YAML** for .NET or Java to `.github/workflows/` in your repository.
4. **Adjust inputs** (`sln_file`, `project_path`, `build_tool`, etc.) according to your repository structure.
5. **Push the workflow** to `main` (or target branch) to trigger scans automatically or run manually via **Actions → Run workflow**.

---

## 4. Example Usage

### .NET Scan

```yaml
with:
  sln_file: "ECoupon_MAF.sln"
  project_path: "."
```

### Java Scan (Maven)

```yaml
with:
  build_tool: "maven"
  project_path: "src"
  artifact_pattern: "**/target/*.jar"
```

---

## 5. Notes

- The reusable workflow **automatically installs Veracode CLI**, uploads artifacts, and monitors scan status.
- Make sure your **build artifacts are generated** before the scan step (the reusable workflow usually handles building for Java/.NET if paths are correct).
- You can **schedule scans** with cron or trigger manually using workflow dispatch.
