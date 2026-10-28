# FestPass — Docker & Container Security Assessment

**Registration Number:** 202401100100261
**Host Port:** 8061
**Docker Hub Username:** tanyabhadana07
**Docker Hub Repository:** https://hub.docker.com/r/tanyabhadana07/fest-backend

## Project Overview

FestPass is a containerized event-registration application developed for a practical assessment covering Docker, Docker Compose, container networking, image security scanning, image publishing, and image signing.

## Architecture

- **Frontend:** Nginx
- **Backend:** Python and Flask
- **Database:** PostgreSQL
- **Container orchestration:** Docker Compose
- **Vulnerability scanning:** Trivy
- **Image signing and verification:** Cosign
- **Image registry:** Docker Hub

The frontend and backend share the `frontend_net` network. The backend and database share the internal `backend_net` network. The frontend cannot directly resolve or access the database service.

## Security Implementation

- Used a multi-service Docker Compose configuration.
- Configured a PostgreSQL health check and backend startup dependency.
- Stored the database password in a local `.env` file excluded from Git.
- Used a named PostgreSQL volume for data persistence.
- Built a reduced-size Python slim image and ran the backend as a non-root user.
- Scanned images with Trivy and saved reports under `reports/`.
- Published versioned backend images to Docker Hub.
- Signed the `1.0.0` image by digest and verified it with the committed public key.
- Kept the Cosign private key (`cosign.key`) out of Git.
- Tested that verification of the unsigned `1.0.1` image fails.

## Vulnerability and Image-Size Comparison

| Measurement | `fest-backend:0.1` | `fest-backend:0.3` |
|---|---:|---:|
| OS findings reported by Trivy | 2477 | 61 |
| OS HIGH findings | 2243 | 59 |
| OS CRITICAL findings | 234 | 2 |
| Python HIGH findings | 6 | 3 |
| Python CRITICAL findings | 0 | 0 |
| Image content size | 401 MB | 71.8 MB |

The hardened image reduced the reported OS findings and image content size substantially. Two OS-level CRITICAL findings remained in the full scan; the configured `--ignore-unfixed` gate passed because those findings had no available fix according to the scan. This does **not** mean the image has zero CRITICAL findings.

## Published Image

- **Version:** `1.0.0`
- **Docker Hub image:** `tanyabhadana07/fest-backend:1.0.0`
- **Digest:** `sha256:e218feacb21eebf03d81d8e5ea004e23808eebbe609407cdd71bb603e956f7f7`
- **Signature:** Signed with Cosign and verified against `cosign.pub`.

An additional `1.0.1` image was pushed without a signature to demonstrate that Cosign verification rejects an unsigned image.

## Running the Application

1. Install Docker Desktop.
2. Create a local `.env` file containing `DB_PASSWORD` with a strong password.
3. Set `PORT=8061` and `REGNO=202401100100261` in your shell if required by your environment.
4. Start the stack with:

   ```bash
   docker compose up -d
   ```

5. Open `http://localhost:8061`.
6. Stop the stack while preserving database data with:

   ```bash
   docker compose down
   ```

Do not commit `.env` or `cosign.key`.

## Evidence

Assessment screenshots are provided in the `screenshots/` directory. Trivy scan reports are provided in the `reports/` directory.

## AI Tools

AI assistance was used for troubleshooting, command guidance, and documentation support. All commands, configurations, and evidence should be reviewed against the actual implementation.

## Author

Registration Number: 202401100100261