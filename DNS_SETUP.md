# DNS setup for zoomcc.com on GitHub Pages

As of 2026-09-28 the domain's nameservers are `ns1.justhost.com` / `ns2.justhost.com`,
i.e. DNS is still managed by the old (now suspended) Justhost hosting account.
The registrar is eNom (Justhost/Bluehost resell eNom domains), and the domain is
paid up through 2027-07-21.

Until the records below are in place, `zoomcc.com` keeps pointing at Justhost's
"Page cannot be displayed" error page.

## Records to ADD or CHANGE (website)

| Type  | Host  | Value                   |
|-------|-------|-------------------------|
| A     | @     | 185.199.108.153         |
| A     | @     | 185.199.109.153         |
| A     | @     | 185.199.110.153         |
| A     | @     | 185.199.111.153         |
| AAAA  | @     | 2606:50c0:8000::153     |
| AAAA  | @     | 2606:50c0:8001::153     |
| AAAA  | @     | 2606:50c0:8002::153     |
| AAAA  | @     | 2606:50c0:8003::153     |
| CNAME | www   | jlovell.github.io       |

Delete the existing `A @ 173.254.30.34` record (that is the Justhost server).
The `www` record is currently `CNAME www -> zoomcc.com`; replace it with the
`jlovell.github.io` CNAME above so GitHub can redirect www to the apex.

## Records to KEEP exactly as they are (email — Google Workspace)

| Type | Host                 | Value                                   |
|------|----------------------|-----------------------------------------|
| MX   | @                    | 1  aspmx.l.google.com                   |
| MX   | @                    | 5  alt1.aspmx.l.google.com              |
| MX   | @                    | 5  alt2.aspmx.l.google.com              |
| MX   | @                    | 10 aspmx2.googlemail.com                |
| MX   | @                    | 10 aspmx3.googlemail.com                |
| TXT  | default._domainkey   | k=rsa; p=MHwwDQYJKoZIhvcNAQEBBQADawAwaAJhAMRgzhuRfdFGKKJXIDNSu9s7R4F0580vfgMDY6B3xKmVOioi2ezGjHE/w3TnQC5lA1V8oJgq8IqcrP7zTYwyjZ/6GJ5kehDwDheJWuTy25Zo+5if3+yUEidBpGSR18hEcwIDAQAB |

### SPF (recommended change)

The current SPF record is:

    v=spf1 +a +mx +ip4:173.254.28.35 ?all

`+a` and the `ip4` both point at Justhost servers that no longer send your mail.
Once the A records point at GitHub, `+a` would authorize GitHub's IPs to send
mail as you, which is wrong. Replace it with Google's recommended record:

    v=spf1 include:_spf.google.com ~all

## Records that can be dropped (they all pointed at the dead Justhost box)

`mail`, `ftp`, `cpanel`, `webmail`, `autoconfig` -> 173.254.30.34

## Recommended: move DNS off Justhost entirely

Because the Justhost account is the thing that failed, leaving the DNS zone there
is fragile. If Justhost deletes the account, the zone disappears and email stops
resolving. Two safe options:

1. Log in to the Justhost/Bluehost account and change the nameservers on the
   domain to a free DNS provider such as Cloudflare, then recreate all the
   records above there. (Domain is registered via eNom under that same account,
   so nameservers are changed from the Justhost domain panel.)
2. Transfer the domain to a registrar you control (Cloudflare Registrar, Porkbun,
   Namecheap). The domain currently has `clientTransferProhibited` set, which
   must be unlocked in the Justhost panel first, and an EPP/auth code obtained.

Either way, add ALL the records above in the new zone BEFORE switching
nameservers so email never goes dark.

## After DNS propagates

1. Open https://github.com/jlovell/zoomcc.com/settings/pages
2. Confirm "zoomcc.com" shows a green DNS check.
3. Tick "Enforce HTTPS" once GitHub has issued the certificate (usually within
   an hour of DNS propagating). Or run:

       gh api -X PUT repos/jlovell/zoomcc.com/pages -F https_enforced=true

4. Optional but recommended: verify the domain at
   https://github.com/settings/pages so no one else can claim it on GitHub Pages.
