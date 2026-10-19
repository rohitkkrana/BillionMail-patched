# BillionMail Patched Version

Custom fixes for DNS verification with dedicated outbound IP addresses.

## Patch 1: Dedicated-IP TXT/SPF DNS verification

- Verify the SPF TXT record using the configured dedicated outbound IP.
- Avoid reusing a shared-IP SPF cache result for dedicated-IP domains.
- Validate the record against the correct IP context.

## Patch 2: Dedicated-IP A record refresh

- Bypass the cached A-record result when a dedicated IP is configured.
- Perform immediate validation during refresh against the configured dedicated IP.

## Patch 3: PTR record verification and refresh

- Use the domain's active dedicated outbound IP for PTR verification.
- Resolve the active outbound IP during PTR refresh.
- Fall back to the default server IP when no dedicated IP is supplied.

## Testing

From the core directory, run:

    go test ./internal/service/domains

The host may require a Go-enabled Docker build environment to run this test.

## Security

Do not commit production credentials, private keys, .env files,
mailbox data, or server-specific backups.
