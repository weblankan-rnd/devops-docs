# Next.js Deployment (Docker, GHCR, SSH)

GitHub Actions workflow to build and deploy a Next.js project to a remote server via Docker/Compose, pushing images to GitHub Container Registry (GHCR).

## Prerequisites
- Dockerfile per environment under `docker/<env>/Dockerfile` (e.g., `docker/live/Dockerfile`).
- Remote server has Docker + Docker Compose and path `/opt/<project_name>/docker/<env>/` with a `docker-compose.yml` that pulls the GHCR image.
- SSH key added to repo secret `PRIVATE_KEY` (matches `ssh_user` on target host).
- `GITHUB_TOKEN` available (built-in) for GHCR push/login.

## Required Secrets
- `PRIVATE_KEY`: SSH private key for the target server user.

## Optional/Configurable env values in the workflow
- `build_env`: `staging` or `live` (controls Dockerfile path and image tag).
- `ssh_user`: SSH user (default `root`).
- `ssh_host`: Target host.
- `project_name`: Folder name under `/opt/` and image name stem.

## Workflow YAML
```yaml
name: Deploy to main

# nextjs project deployment workflow for staging/live
# change ssh_host, ssh_user, if needed
# add ssh key to repo secrets as PRIVATE_KEY

on:
  push:
    branches:
      - main
  workflow_dispatch:

env:
  build_env: live # staging or live
  ssh_user: root
  ssh_host: 165.22.54.114
  project_name: next-esu

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

      - name: Copy files to server
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
            docker login ghcr.io -u ${{ github.actor }} -p ${{ secrets.GITHUB_TOKEN }}
            cd /opt/${{ env.project_name }}/docker/${{ env.build_env }} && docker compose pull && docker compose up -d --remove-orphans && docker image prune -af

  clean:
    runs-on: ubuntu-latest
    needs: deploy
    name: clean
    steps:
      - uses: snok/container-retention-policy@v3.0.0
        with:
          account: weblankan-next
          token: ${{ secrets.GITHUB_TOKEN }}
          image-names: ${{ env.project_name }}
          cut-off: 7d
          keep-n-most-recent: 5
          dry-run: false
```

## How to deploy with this action
1. Add secret `PRIVATE_KEY` (SSH key that can write to `/opt/<project_name>`).
2. Update `ssh_host`, `ssh_user`, `project_name`, and `build_env` if needed.
3. Ensure `/opt/<project_name>/docker/<env>/docker-compose.yml` uses the GHCR image tags `${build_env}` or `${{ github.sha }}` as needed.
4. Push to `main` or trigger `Run workflow` manually; workflow builds image, pushes to GHCR, syncs `docker/` to server, then runs `docker compose pull && up -d`.
5. Confirm containers are healthy on the server; logs via `docker compose logs -f` in `/opt/<project_name>/docker/<env>/`.
