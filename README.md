# xve-artifacts

A GitHub repository dedicated to storing and distributing the pre-built WSL2 distribution tarball (`xve-distro.tar`) as GitHub Release assets.

## Purpose

* Centralize a portable development environment archive for easy consumption by the team.
* Avoid bloating application repos with large binaries.
* Leverage GitHub’s Releases feature to track versioned environment snapshots.

## Repository Structure

* **Releases**: Each release (e.g., `v1.0.0`, `export-2025-06-21_12-00`) contains a single asset:

  * `xve-distro.tar` — a WSL2 distro filesystem export ready for import.
* **Branches**: No code branches—this repo hosts only release artifacts.

## How to Download

### Using GitHub CLI

```bash
# Download a specific release asset by tag:
gh release download <tag> --repo your-org/xve-artifacts --pattern "xve-distro.tar"

# Example: download the latest export:
gh release download latest --repo your-org/xve-artifacts --pattern "xve-distro.tar"
```

### Using PowerShell

```powershell
$tag = 'export-2025-06-21_12-00'
Invoke-RestMethod -Uri "https://api.github.com/repos/your-org/xve-artifacts/releases/tags/$tag" -Headers @{ 'User-Agent' = 'XVE-Importer' } |
  Select-Object -ExpandProperty assets |
  Where-Object { $_.name -eq 'xve-distro.tar' } |
  ForEach-Object {
    Invoke-WebRequest -Uri $_.browser_download_url -OutFile '.\xve-distro.tar'
  }
```

Or simply visit the **Releases** tab on the GitHub web UI and click **Download**.

## Versioning

* Release tags follow the pattern `export-YYYY-MM-DD_HH-MM` by default.
* You can also create semantic version tags (e.g., `v1.0.0`) if you prefer a stable versioning scheme.

## Uploading New Artifacts

To publish a new `xve-distro.tar`:

1. Build and export the tarball using the **windows-powershell-wsl-distro-generator**.
2. Generate or update a release (e.g., with GitHub CLI):

   ```bash
   # Create a new release:
   gh release create export-$(date +'%Y-%m-%d_%H-%M') ./xve-distro.tar \
     --repo your-org/xve-artifacts \
     --title "XVE Distro $(date +'%Y-%m-%d_%H-%M')" \
     --notes "Automated export"

   # Or upload to an existing tag:
   gh release upload latest ./xve-distro.tar --repo your-org/xve-artifacts --clobber
   ```
3. Verify the asset appears under the **Releases** tab.

## Access Control

* This repository can be **public** for easy, anonymous download of development environments.
* If you want to restrict access, make the repo **private**; consumers will need a GitHub PAT with `repo` scope.

## Best Practices

* Keep releases lightweight: include only the `.tar` asset, no source or code.
* Tag releases consistently to enable easy automation and discovery.
* Document any changes to the environment (e.g., PHP version bumps) in the release notes.

---

*For details on building and exporting the distro, see: [windows-powershell-wsl-distro-generator](https://github.com/your-org/windows-powershell-wsl-distro-generator).*
