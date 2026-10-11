# personal-mail-server

A self-hosted email server running [Docker Mailserver](https://docker-mailserver.github.io/docker-mailserver/) (Postfix + Dovecot), with spam filtering and antivirus, managed through a small set of shell scripts.

## Components

- **Docker Mailserver** — Postfix (SMTP) and Dovecot (IMAP), plus DKIM/DMARC tooling
- **Rspamd** — spam filtering
- **ClamAV** — antivirus
- **Fail2Ban** — intrusion prevention

## Requirements

- A host with a public IP and a domain whose MX record points at it
- Ports 25, 143, 465, 587 and 993 reachable (port 25 in particular is often blocked by cloud providers)
- Docker and Docker Compose

## Getting started

1. Edit `docker-compose.yml` and set `hostname` to your mail server's FQDN (your MX target).
2. Start the stack:

   ```bash
   ./scripts/start.sh
   ```

3. Add a mailbox:

   ```bash
   ./scripts/add_user.sh user@example.com 'password'
   ```

## Scripts

| Script | Purpose |
| --- | --- |
| `start.sh` | `docker compose up -d` |
| `stop.sh` | `docker compose down` |
| `status.sh` | `docker ps` |
| `logs.sh <container>` | Follow container logs |
| `add_user.sh <email> <password>` | Create a mailbox |

## Ports (host : container)

| 2525 : 25 | SMTP |
| 587 : 587 | Submission |
| 465 : 465 | SMTPS |
| 1143 : 143 | IMAP |
| 993 : 993 | IMAPS |

## Configuration & secrets

- `.env` (gitignored) holds credentials — see `.env.example` for the expected variables
- `config/` is mounted into the container at `/tmp/docker-mailserver`; account files such as `postfix-accounts.cf` are gitignored
- Mail data, state, and logs are stored in named Docker volumes

## Notes

- Deliverability depends on correct DNS: MX, SPF, DKIM and DMARC records, plus a valid TLS certificate (Docker Mailserver provisions Let's Encrypt by default).
