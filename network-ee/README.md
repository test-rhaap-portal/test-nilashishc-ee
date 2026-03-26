# Execution Environment Project

This is a sample execution environment project to build and publish your EE.

## Directory Structure

```shell
├── .github
│   └── workflows
│       └── ee-build.yml    # CI/CD workflow for building and publishing
├── .gitignore
├── README.md
└── network-ee.yml
```

## CI/CD Workflow Features

The included GitHub Actions workflow (`ee-build.yml`) provides:

### Token Validation

- Validates configured tokens before starting builds
- Fails fast if credentials are missing

### Base Image Lifecycle Checks

- Warns if the base image is older than 40 days
- Fails if the base image is older than 80 days
- Helps ensure your EE stays up-to-date with security patches

### Galaxy Server Support

- Supports authenticated Galaxy server access via token secrets
- Falls back to public Galaxy if no token is configured

### Production Release Workflow

- Automatic tagging on release
- Preserves previous production image as `previous` tag for rollback
- Supports semantic versioning

## Required Secrets and Variables

### Secrets (Repository Settings > Secrets)

| Secret | Required | Description |
|--------|----------|-------------|
| `ANSIBLE_GALAXY_SERVER_PRIVATE_HUB_VALIDATED_TOKEN` | Yes | Galaxy server authentication token |
| `ANSIBLE_GALAXY_SERVER_PRIVATE_HUB_PUBLISHED_TOKEN` | Yes | Galaxy server authentication token |
| `ANSIBLE_GALAXY_SERVER_PRIVATE_HUB_RH_CERTIFIED_TOKEN` | Yes | Galaxy server authentication token |
| `REGISTRY_USERNAME` | No | Container registry username (defaults to `github.actor`) |
| `REGISTRY_PASSWORD` | No | Container registry password (defaults to `GITHUB_TOKEN`) |
| `REDHAT_REGISTRY_PASSWORD` | No | Red Hat registry password (for pulling base images) |

### Variables (Repository Settings > Variables)

| Variable | Default | Description |
|----------|---------|-------------|
| `EE_REGISTRY` | `ghcr.io` | Container registry hostname |
| `EE_IMAGE_NAME` | `<owner>/<repo>` | Image name (GHCR requires the `owner/repo` namespace) |
| `REDHAT_REGISTRY_USERNAME` | - | Red Hat registry username |

See `NEXT_STEPS.md` for a complete setup checklist.

## Usage

### Building Locally

```bash
# Install ansible-builder
pip install ansible-builder

# Create build context
ansible-builder create --file network-ee.yml

# Build the image
podman build -t my-ee:latest context/
```

### Triggering CI/CD

- **Pull Request**: Builds and tests the EE (no push to registry)
- **Push to main**: Builds, tests, and pushes with `latest` and commit SHA tags
- **Release**: Tags image with version number and `prd` tag

## Customization

Edit `network-ee.yml` to customize:

- Base image
- Ansible collections
- Python dependencies
- System packages

See the [ansible-builder documentation](https://ansible.readthedocs.io/projects/builder/en/latest/)
for more details on execution environment configuration.
