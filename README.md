# BillionMail Patched

A community-maintained fork of [aaPanel/BillionMail](https://github.com/aaPanel/BillionMail) with targeted fixes for DNS verification and refresh when using dedicated outbound IP addresses.

BillionMail is an open-source mail server and email marketing platform. This fork focuses on improving dedicated-IP DNS record verification without replacing the original BillionMail architecture.

## Major Changes

### 1. Dedicated-IP SPF/TXT Verification

- Verify the domain's SPF TXT record using the configured dedicated outbound IP.
- Avoid reusing a shared-IP SPF cache result for dedicated-IP domains.
- Perform verification in the correct dedicated-IP context.

### 2. Dedicated-IP A Record Refresh

- Bypass the cached A-record result when a dedicated IP is configured.
- Perform immediate A-record validation against the configured dedicated IP during refresh.

### 3. Dedicated-IP PTR Verification and Refresh

- Use the domain's active dedicated outbound IP for PTR verification.
- Resolve the active outbound IP during PTR refresh.
- Avoid using a cached PTR result for dedicated-IP verification.
- Fall back to the default server IP when no dedicated IP is provided.

These changes target DNS verification and cache-refresh behavior. They do not guarantee successful email delivery: correct DNS configuration, reverse DNS/PTR setup, SPF, DKIM, DMARC, IP reputation, and provider policies still matter.

## Installation

**Recommended: install this fork on a fresh server or test environment first.**

### Requirements

- A supported Linux server for BillionMail.
- Git and Docker, with the Docker Compose plugin if using the Docker installation method.
- A domain you control and the required DNS records.
- Appropriate server and DNS configuration for outbound mail.

Check the upstream documentation for current system requirements and installation prerequisites.

### Method 1: Standard installation

Clone the patched branch:

```bash
cd /opt
git clone -b dedicated-ip-dns-fixes https://github.com/rohitkkrana/BillionMail-patched.git
cd BillionMail-patched
bash install.sh
```

### Method 2: Docker Compose

```bash
cd /opt
git clone -b dedicated-ip-dns-fixes https://github.com/rohitkkrana/BillionMail-patched.git
cd BillionMail-patched
cp env_init .env
```

Review and configure `.env` according to your environment, then run:

```bash
docker compose up -d
```

Follow the upstream documentation for any additional setup required by your server.

**Important:** Do not run a fresh installation over an existing production deployment without a tested backup and upgrade plan.

## Testing

The dedicated-IP DNS service tests can be run from the `core` directory with Go installed:

```bash
cd core
go test ./internal/service/domains
```

Alternatively, use a Go-enabled Docker image:

```bash
docker run --rm \
  -v "$PWD/core:/src" \
  -w /src \
  golang:1.24 \
  go test ./internal/service/domains
```

This command tests the DNS service package. It does not, by itself, prove that every DNS provider or production mail-delivery scenario works correctly.

## Management

After installation, the available management commands may include:

```bash
bm help
bm default
bm show-record
bm update
```

Use `bm help` and the upstream documentation to confirm the commands supported by your installed version.

## Upstream Project and Documentation

- **Original project:** https://github.com/aaPanel/BillionMail
- **Upstream documentation:** https://www.billionmail.com/
- **Upstream issues:** https://github.com/aaPanel/BillionMail/issues
- **License:** See the repository's `LICENSE` file.

## Contributing

Bug reports, reproducible test cases, and improvements are welcome. Please describe the affected DNS record, whether a dedicated outbound IP is configured, and the steps required to reproduce the issue. Remove private IP details, credentials, tokens, and other sensitive server information from logs before sharing them.

## Security

- Never commit `.env` files, credentials, private keys, mailbox data, production backups, or server-specific secrets.
- Test changes before deploying them to a production mail server.
- Review upstream changes before merging or updating this fork.

## License and Attribution

BillionMail is distributed under the license specified in the upstream repository. Retain the upstream license and copyright notices when redistributing this fork, and review the repository's `LICENSE` file for applicable obligations.

This fork contains custom DNS verification and refresh fixes maintained separately from the upstream project.
