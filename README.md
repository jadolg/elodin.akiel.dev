# elodin.akiel.dev

Ansible that turns one Debian box into a public, ad-blocking DNS resolver.

It installs [elodin](https://deb.akiel.dev/) and serves DNS-over-TLS on 853 and
DNS-over-HTTPS on 443, forwarding upstream to Cloudflare and Quad9 over TLS,
with DNSSEC validation and a dozen or so blocklists applied. Certificates come
from Let's Encrypt via Lego, renewed on a timer, and elodin picks up the new
ones without anyone logging in.

The rest is the boring stuff a public box needs: an nftables ruleset that only
lets SSH, 853 and 443 through - and bounds how many connections one source may
hold on the latter two, and how fast it may open them - fail2ban, SSH, sysctl
and journald hardening (query logging is on, so the journal is capped and
rotated rather than left to grow unbounded during a flood), and a Grafana
Alloy agent shipping logs and metrics home over the VPN.

`mise run deploy` applies it. Pushes to `main` do the same from GitHub Actions.

## Using it

Point your device at `elodin.akiel.dev`. Use DNS-over-TLS where a setting asks
for a hostname, DNS-over-HTTPS where it asks for a URL:

- DoT: `elodin.akiel.dev` (port 853)
- DoH: `https://elodin.akiel.dev/dns-query`

**Firefox** - Settings → Privacy & Security → DNS over HTTPS → *Max Protection*
→ *Custom*, and paste the DoH URL.

**Chrome / Edge / Brave** - Settings → Privacy and security → Security → *Use
secure DNS* → *With: Custom*, and paste the DoH URL.

**Android 9+** - Settings → Network & internet → Private DNS → *Private DNS
provider hostname*, enter `elodin.akiel.dev`. This covers all apps and both
Wi-Fi and mobile data.

**iPhone / iPad / Mac** - iOS and macOS have no built-in field for this, so
use a configuration profile: [download the profile](https://elodin.akiel.dev/apple-doh.mobileconfig)
and install it (iOS: Settings → *Profile Downloaded*; macOS: System Settings →
Privacy & Security → Profiles).

To check it works, visit `doubleclick.net`; it should fail to load.
