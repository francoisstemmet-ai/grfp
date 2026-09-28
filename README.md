# grfp.co.za

Static website for GRFP, hosted on Plesk at `41.185.114.18`, with mail provided by Plesk and DNS authoritative at 1Grid.

## Stack

- Static HTML/CSS/JS, no build step or dependencies
- Apache config via `.htaccess`, served from Plesk `httpdocs`
- TLS via Let's Encrypt on Plesk

## Repository layout

```
grfp.co.za/
├── .htaccess              # Plesk/Apache config (no redirects; Plesk handles HTTPS + preferred domain)
├── index.html             # Main page
├── 404.html               # Not-found page
├── robots.txt
├── sitemap.xml
└── assets/
    ├── css/style.css
    ├── js/app.js
    └── img/               # GRFP-Logo.png, favicon.svg, hero_bg.jpg
```

The deployable archive is a flat zip whose contents map directly onto the Plesk `httpdocs` directory, so asset paths in `index.html` and `404.html` are relative (`assets/...`).

## Deployment

1. Build or refresh the flat archive from the contents of `grfp.co.za/`.
2. Upload and extract into the Plesk `httpdocs` directory for the `grfp.co.za` website.
3. Confirm `.htaccess` is present in `httpdocs`; Plesk controls HTTPS redirection and the `www` → apex redirect, so no redirect rules belong in `.htaccess`.
4. Verify in an incognito window that the padlock shows and no mixed-content warnings appear.

## Mail

Mailbox: `marco@grfp.co.za` (Plesk Mail service enabled, Roundcube webmail).

Client settings as shown by Plesk:

| Setting | Value |
| --- | --- |
| Incoming server | `grfp.co.za` |
| Outgoing server | `grfp.co.za` |
| Username | `marco@grfp.co.za` |
| IMAP | `993` |
| POP3 | `995` |
| SMTP | `465` |

Plesk's client hostname is the apex domain, so clients configured with `grfp.co.za` do not depend on the `imap.`/`smtp.` CNAMEs.

## DNS (1Grid authoritative)

1Grid holds the authoritative nameservers, so all public DNS changes are made in the 1Grid panel, not in Plesk. Plesk's DNS page is not authoritative for this domain.

| Host | Type | Value |
| --- | --- | --- |
| `grfp.co.za` | A | `41.185.114.18` |
| `www.grfp.co.za` | CNAME | `grfp.co.za` |
| `ftp.grfp.co.za` | CNAME | `grfp.co.za` |
| `webmail.grfp.co.za` | CNAME | `grfp.co.za` |
| `mail.grfp.co.za` | A | `41.185.114.18` |
| `imap.grfp.co.za` | CNAME | `mail.grfp.co.za` |
| `smtp.grfp.co.za` | CNAME | `mail.grfp.co.za` |
| `pop.grfp.co.za` | CNAME | `mail.grfp.co.za` |
| `grfp.co.za` | MX (10) | `mail.grfp.co.za` |
| `grfp.co.za` | TXT | `v=spf1 ip4:41.185.114.18 include:relay.mailchannels.net ~all` |
| `_dmarc.grfp.co.za` | TXT | `v=DMARC1; p=quarantine; adkim=s; aspf=s` |
| `default._domainkey.grfp.co.za` | TXT | Plesk DKIM public key |
| `_domainkey.grfp.co.za` | TXT | `o=-` |
| `_imaps._tcp.grfp.co.za` | SRV | `grfp.co.za` |
| `_pop3s._tcp.grfp.co.za` | SRV | `grfp.co.za` |
| `_smtps._tcp.grfp.co.za` | SRV | `grfp.co.za` |

Nameservers: `thor.ns.1-grid.net`, `thor.ns.1-grid.com`, `thor.ns.1-grid.co.za`, `thor.ns.1-grid.co.uk`.

Note: earlier the MX records pointed at `1-grid-mx01..04` with an SPF authorizing `41.185.114.10`. Those were replaced when mail moved to Plesk on `41.185.114.18`.

## Outstanding

- **PTR/rDNS for `41.185.114.18`.** It currently resolves to `lnxsvlrweb09.hostserv.co.za`, a web hostname rather than a mail hostname. Worth asking HostServ to change it to a mail hostname for better outbound deliverability and to avoid rejections by receiving servers.
- **Domain registrar move.** The domain is not yet showing as active in 1Grid, so the registrar change is deferred. When it happens, only the NS set at the current registrar changes; MX, SPF, DKIM, and DMARC stay as listed above.
