# atrifact_storage

This repository provides a collection of tools and configurations to deploy various solutions for storing and managing DevOps artifacts. The goal is to offer comparable environments for testing and deploying private repositories for binaries, container images, language packages, and other artifacts.

## Repository Contents

| Application       | Artifact Types                 | Deployment Method | Notes                   |
| ----------------- | ------------------------------ | ----------------- | ----------------------- |
| JFrog Artifactory | Docker, Maven, npm, PyPI, Helm | Docker Compose    | Community Edition       |



## Directory Structure

artifact_storage
\
├── [Artifactory](Artifactory/)


## How to Use

1. Navigate to the directory of the desired application.
2. Launch the environment following the provided instructions (`docker-compose up`, `helm install`, `ansible-playbook`).
3. Configure users and repositories as needed.

## License

MIT License