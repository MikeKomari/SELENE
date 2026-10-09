# SELENE - Secure Engineering Layer for Enterprise Network Environments

Super Image for Gitlab Runner Bank Sinarmas SDE4 because I sudah agak lelah

---

**SELENE** is an all-in-one DevOps & CI/CD Swiss Army knife container image designed for CI/CD runners (GitLab Runner, GitHub Actions, local debugging, and automation tasks). It packages essential system administration tools, networking utilities, remote deployment helpers, Docker CLI (with Compose & Buildx), and Kubernetes CLI (`kubectl`) so pipelines don't waste time running repetitive `apt-get install` commands on every run.

## Image Flavors

| Variant | Dockerfile | Base Image | Highlights |
|---|---|---|---|
| **`selene`** (Standard) | `Dockerfile` | `debian:bookworm-slim` | Lean environment with all essential CLI tools, Docker, and `kubectl`. |
| **`selene-wp`** (With Python) | `Dockerfile.wp` | `python:3.12-slim-bookworm` | Includes full Python 3.12 runtime alongside the complete CLI and container toolset. |

## Included Tooling & Packages

- **Containerization & Orchestration:**
  - `docker-ce-cli`
  - `docker-buildx-plugin`
  - `docker-compose-plugin`
  - `kubectl` (latest stable release)
- **Networking & Diagnostics:**
  - `curl`, `wget`, `netcat-openbsd`, `dnsutils`, `iputils-ping`
- **Automation & Remote Execution:**
  - `openssh-client`, `sshpass`, `rsync`
- **Utilities & Archiving:**
  - `bash`, `git`, `jq`, `gettext-base` (`envsubst`), `tar`, `unzip`, `zip`, `gnupg`, `ca-certificates`
- **Python Runtime (`selene-wp` only):**
  - Python 3.12, `pip`

## Quick Start

### Pull and Run

```bash
# Run standard flavor
docker run --rm -it <your-dockerhub-username>/selene:latest

# Run Python-enabled flavor
docker run --rm -it <your-dockerhub-username>/selene-wp:latest
```

### Local Build

To build the images locally:

```bash
# Standard image
docker build -t selene:latest -f Dockerfile .

# With Python (wp)
docker build -t selene-wp:latest -f Dockerfile.wp .
```

### Usage Examples

#### In GitLab CI (`.gitlab-ci.yml`)

```yaml
deploy_job:
  image: <your-dockerhub-username>/selene:latest
  script:
    - kubectl version --client
    - envsubst < k8s-deployment.template.yaml | kubectl apply -f -
    - rsync -avz ./dist/ user@remote-host:/var/www/
```

#### In GitHub Actions

```yaml
jobs:
  run-task:
    runs-on: ubuntu-latest
    container:
      image: <your-dockerhub-username>/selene-wp:latest
    steps:
      - uses: actions/checkout@v4
      - name: Verify Tools
        run: |
          docker --version
          docker compose version
          kubectl version --client
          python3 --version
```

## CI/CD Workflow (GitHub Actions)

A GitHub Actions workflow is provided in `.github/workflows/docker-publish.yml` to automatically build and push multi-tag images (`latest`, short SHA, git tags) to Docker Hub.

To enable automated builds:
1. Go to your GitHub repository **Settings** &rarr; **Secrets and variables** &rarr; **Actions**.
2. Add the following repository secrets:
   - `DOCKERHUB_USERNAME`: Your Docker Hub username.
   - `DOCKERHUB_TOKEN`: A Personal Access Token (PAT) generated from Docker Hub with Read & Write permissions.
