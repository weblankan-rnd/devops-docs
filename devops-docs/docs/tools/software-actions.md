# Next.js Staging Deployment (Docker, GHCR, SSH)

GitHub Actions workflow to build and deploy a Next.js project to a staging server using Docker/Compose with images stored in GitHub Container Registry (GHCR).

## Prerequisites
- Dockerfile at `docker/staging/Dockerfile` (and optional `docker/live/Dockerfile` if reused).
- Remote server has Docker + Docker Compose and path `/opt/<project_name>/docker/staging/` containing `docker-compose.yml` referencing the GHCR image tag `staging` (or commit SHA).
- SSH key added to repo secret `PRIVATE_KEY` for the target user.
- Optional: `DOCKER_USER` and `DOCKER_PASSWORD` secrets if not using `GITHUB_TOKEN` for registry auth.

## Required secrets
- `PRIVATE_KEY`: SSH private key for target host user.
- `GITHUB_TOKEN`: Built-in; used for GHCR push/login in build job.
- `DOCKER_USER`, `DOCKER_PASSWORD`: Used on the server during deploy login (per provided workflow).

## Workflow YAML
```yaml
name: Deploy to staging server
# nextjs project deployment workflow for staging
# change ssh_host, ssh_user if needed
# add ssh key to repo secrets as PRIVATE_KEY

on:
  push:
    branches:
      - staging
  workflow_dispatch:

env:
  build_env: staging # staging or live
  ssh_user: root
  ssh_host: 202.124.164.163
  project_name: david-pieris-gwm

permissions:
  contents: write
  packages: write
  id-token: write
  pull-requests: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build Docker image
        run: |
          PROJECT_NAME=$(basename ${{ github.repository }})
          docker build \
            -f ./docker/${{ env.build_env }}/Dockerfile \
            -t ghcr.io/${{ github.repository_owner }}/${PROJECT_NAME}:${{ env.build_env }} \
            -t ghcr.io/${{ github.repository_owner }}/${PROJECT_NAME}:${{ github.sha }} .

      - name: Push Docker image
        run: |
          PROJECT_NAME=$(basename ${{ github.repository }})
          docker push ghcr.io/${{ github.repository_owner }}/${PROJECT_NAME}:${{ env.build_env }}
          docker push ghcr.io/${{ github.repository_owner }}/${PROJECT_NAME}:${{ github.sha }}

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: copy files to server
        uses: wlixcc/SFTP-Deploy-Action@v1.2.6
        with:
          username: "${{ env.ssh_user }}"
          server: "${{ env.ssh_host }}"
          ssh_private_key: ${{ secrets.PRIVATE_KEY }}
          local_path: "./docker/*"
          remote_path: "/opt/${{ env.project_name }}/docker/"
          delete_remote_files: true
          sftpArgs: "-o ConnectTimeout=5"

      - name: Execute SSH commands on remote server
        uses: JimCronqvist/action-ssh@master
        with:
          hosts: "${{ env.ssh_user }}@${{ env.ssh_host }}"
          privateKey: ${{ secrets.PRIVATE_KEY }}
          command: |
            docker login ghcr.io -u ${{ secrets.DOCKER_USER }} -p ${{ secrets.DOCKER_PASSWORD }}
            cd /opt/${{ env.project_name }}/docker/${{ env.build_env }}
            docker compose up -d --pull always
            docker system prune -af

  create-release:
    runs-on: ubuntu-latest
    needs: deploy
    steps:
      - uses: marvinpinto/action-automatic-releases@latest
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          prerelease: false
          automatic_release_tag: "v1.0.${{ github.run_number }}"
          title: "Release for commit ${{ github.sha }}"

Optional cleanup job (disabled)
 clean:
   runs-on: ubuntu-latest
   needs: create-release
   name: clean
   steps:
     - uses: snok/container-retention-policy@v3.0.0
       with:
         account: weblankan-lk
         token: ${{ secrets.GITHUB_TOKEN }}
         image-names: ${{ env.project_name }}
         cut-off: 7d
         keep-n-most-recent: 5
         dry-run: false
```

## How to deploy with this action
1. Set secrets: `PRIVATE_KEY`, `DOCKER_USER`, `DOCKER_PASSWORD` (and rely on built-in `GITHUB_TOKEN`).
2. Update `ssh_host`, `ssh_user`, `project_name`, and `build_env` if needed.
3. Ensure `/opt/<project_name>/docker/staging/docker-compose.yml` uses the GHCR image tag `staging` (or commit SHA) pulled in the workflow.
4. Push to the `staging` branch or trigger `workflow_dispatch` to deploy.
5. Verify containers on the server: `docker compose ps` and `docker compose logs -f` in `/opt/<project_name>/docker/staging/`.
