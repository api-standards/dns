# api-standards-dns

DNS-as-code for `api-standards.io`, hosted at [Porkbun](https://porkbun.com).
Records live in [`zones/api-standards.io.yaml`](zones/api-standards.io.yaml) and
are kept in sync with Porkbun by [octoDNS](https://github.com/octodns/octodns)
via the [octodns-porkbun](https://github.com/major/octodns-porkbun) provider.

Workflow:

1. Edit `zones/api-standards.io.yaml` on a branch and open a PR.
2. `.github/workflows/dns-plan.yml` runs `octodns-sync` in dry-run mode and
   posts the diff as a PR comment.
3. Review, merge.
4. `.github/workflows/dns-apply.yml` runs `octodns-sync --doit` on `main`.

## One-time setup

1. In Porkbun, create an API key + secret key (Account → API Access) and enable
   **API Access** for the `api-standards.io` domain (Domain Management → Details).
2. Add repo secrets `PORKBUN_API_KEY` and `PORKBUN_SECRET_KEY`.
3. (Recommended) Create a `production` GitHub environment with required reviewers.
4. Seed real records (below) before merging anything.

## Seeding real records

```bash
python3 -m venv .venv && source .venv/bin/activate   # Python 3.13+
pip install -r requirements.txt

export PORKBUN_API_KEY=... PORKBUN_SECRET_KEY=...
octodns-dump --config-file=config/octodns.yaml --output-dir=zones --lenient api-standards.io. porkbun
```

## Local dry run

```bash
octodns-validate --config-file=config/octodns.yaml
octodns-sync --config-file=config/octodns.yaml          # plan
octodns-sync --config-file=config/octodns.yaml --doit   # apply
```
