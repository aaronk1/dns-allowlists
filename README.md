# Epic Games DNS allowlists

Allowlist rules for Epic Games launcher, store, accounts, downloads, EOS SDK, and EOS Voice, for use behind DNS filtering.

## Sources

- Epic Games Help, "Which domains need to be whitelisted to reach the Epic servers?": https://www.epicgames.com/help/technical-support-c-36624475/general-support-c-35761596/which-domains-need-to-be-whitelisted-to-reach-the-epic-servers-a15422130
- EOS Developer Docs, "Firewall Considerations": https://dev.epicgames.com/docs/epic-online-services/eos-get-started/firewall-considerations
- EOS Support, "When using a strict firewall, which domains should be added to the allow-list for EOS?": https://eoshelp.epicgames.com/s/article/When-using-a-strict-firewall-which-domains-should-be-added-to-the-allow-list-for-EOS?language=en_US

`allow/epic.txt` has a section for each source. Entries are AdGuard syntax (`@@||host^`), which also matches subdomains.

**Last refreshed:** 2026-10-25, by hand. The Epic Help page returns 403 to automated fetches, so refresh it manually: open each link, compare the hostnames with `allow/epic.txt`, edit, then run `scripts/build.sh`.

## Files

- `allow/epic.txt`: source of truth. Edit this.
- `dist/adguard-allow.txt`: combined AdGuard rules. Subscribe to it as one URL in AdGuard Home.
- `dist/pihole-regex.txt`: Pi-hole regex allowlist, one entry per host and its subdomains.
- `scripts/build.sh`: regenerates `dist/` from `allow/`. Run it before committing.

## Notes

- **Paths dropped.** Some source URLs include paths (e.g. `static-assets-prod.epicgames.com/static/`). DNS filtering can't match paths, so the host is allowed.
- **EOS Voice.** Voice media uses UDP to dynamic AWS IPs, which can't be allowlisted by domain. The docs say Voice has no TCP fallback. Networks that block outbound UDP will break Voice even with these rules. AWS publishes its ranges at https://ip-ranges.amazonaws.com/ip-ranges.json.
- **Telemetry.** `tracking.epicgames.com` and `datarouter.ol.epicgames.com` are telemetry. They're in the Epic Help list, so they're included. Remove them from `allow/epic.txt` if you don't want them.
- **Broader than the docs.** AdGuard's `||` also matches subdomains. `epicgames.com` therefore allows every `*.epicgames.com` host, where the docs list only the apex and specific hosts.
- **dnsmasq not generated.** dnsmasq has no native allowlist, so it can't express these rules without extra setup.
