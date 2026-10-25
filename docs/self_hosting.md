# Self-hosting n8n with Docker Compose

This guide describes how to run a local n8n instance for learning and testing the workflows in this repository. It is intended for a development environment, not a production deployment exposed directly to the public internet.

## Prerequisites

- Docker Desktop, or Docker Engine with the Docker Compose plugin.
- Git and a local copy of this repository.
- Basic familiarity with containers, environment variables, and application security.

Official references:

- [Install n8n with Docker Compose](https://docs.n8n.io/hosting/installation/server-setups/docker-compose/)
- [n8n environment variables](https://docs.n8n.io/hosting/configuration/environment-variables/)
- [n8n security audit](https://docs.n8n.io/hosting/securing/security-audit/)

## 1. Configure the environment

From the repository root, create a local `.env` file from `.env.example`.

On macOS or Linux:

```shell
cp .env.example .env
```

On PowerShell:

```powershell
Copy-Item .env.example .env
```

Edit `.env` and provide values appropriate for your machine. At minimum, review the timezone and any variables referenced by `compose.yaml`. If you configure `N8N_ENCRYPTION_KEY`, use a strong, unique value and keep it stable for this instance.

A value stored in `.env` is only passed to the container when `compose.yaml` references it. Check that each required variable is wired into the n8n service.

**Never commit `.env`.** Commit only `.env.example` with placeholder values and comments. Do not paste real API keys, passwords, tokens, or encryption keys into documentation or issue reports.

## 2. Start n8n

Run the following commands from the directory containing `compose.yaml`:

```shell
docker compose up -d
```

Check the container status:

```shell
docker compose ps
```

Follow the service logs when troubleshooting:

```shell
docker compose logs -f n8n
```

If the service in `compose.yaml` has a different name, use that service name in the logs command.

For a local setup, the interface is normally available at [http://localhost:5678](http://localhost:5678). Complete the initial setup in the browser. If you intentionally change the published port, use that port instead.

## 3. Persistence

The Compose configuration should mount a persistent volume at `/home/node/.n8n`. This directory contains instance data that must survive container recreation, including important configuration and encryption-key material when managed by the instance.

- `docker compose stop` stops the services without removing them.
- `docker compose down` removes the containers and network but normally preserves named volumes.
- `docker compose down --volumes` also removes Compose-managed volumes. **Do not run it unless you intend to delete the persistent instance data.**

Exported workflow JSON files in this repository are not a backup of the whole n8n instance. They do not replace backups of the persistent volume, configuration, and any external database you may later add.

## 4. Import a workflow

1. Open the local n8n editor.
2. Open the workflow menu and choose **Import from File**.
3. Select the workflow's `workflow.json` file.
4. Configure the required credentials in the local instance. Exports intentionally should not contain secret values.
5. Review the nodes and test the workflow with non-sensitive sample data before activating it.

See n8n's [export and import documentation](https://docs.n8n.io/workflows/export-import/) for the current import options.

## 5. Update n8n

Before updating, review the release notes for the target version and back up the persistent data. Then pull the image and recreate the service:

```shell
docker compose pull
docker compose up -d
```

The actual update behavior depends on the image tag configured in `compose.yaml`. Prefer an intentional version-upgrade process over updating blindly, especially if workflows are important. Test the workflows after an update.

## 6. Security checklist

- Bind the editor to localhost for a development-only setup when it does not need to be accessed by other machines. Avoid publishing the editor directly to the public internet.
- For remote access, use a properly configured HTTPS reverse proxy or another secure access layer, and review n8n's security guidance before exposing the service.
- Use n8n's credential store for credentials instead of hard-coding secrets in nodes, expressions, or workflow JSON.
- Keep `.env`, backups, local databases, execution data, and logs out of Git.
- Use synthetic or sanitized data when testing and documenting workflows.
- Keep n8n and the host system updated, and restrict who can access the instance.
- Back up the instance before making configuration or version changes.

Self-hosting requires ongoing attention to security, updates, storage, and backups. For more guidance, review the official [n8n hosting documentation](https://docs.n8n.io/hosting/).
