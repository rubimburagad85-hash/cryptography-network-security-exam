# Firewall test evidence (`filter_tests.md`)

> **Complete this file from YOUR laboratory run.** Replace every `<...>` and every "ACTUAL" cell with real output
> (copy-paste the terminal text or attach a screenshot in `evidence/`). Do not invent results – the assessor may ask you to repeat a test live.

## Lab topology (fill in)

| Item | Value |
|------|-------|
| Records server IP | `<SERVER_IP>` |
| Service tested (given by assessor) | `<tcp/PORT, e.g. tcp/443>` |
| Staff client (permitted) | `<STAFF_CLIENT_IP>` in `<STAFF_NET>` |
| Guest client (blocked) | `<GUEST_CLIENT_IP>` in `<GUEST_NET>` |
| Other/external client (blocked) | `<OTHER_CLIENT_IP>` |
| Firewall tool | iptables (script: `firewall/records_server_firewall.sh`) |

## Applying the rules (on the server / gateway)

```bash
sudo ./firewall/records_server_firewall.sh apply
sudo ./firewall/records_server_firewall.sh show      # paste this output below
```

ACTUAL `show` output:
```
<paste here>
```

## Tests

| # | Test | Run on | Command | Expected outcome | Actual result |
|---|------|--------|---------|------------------|---------------|
| T1 | **PERMITTED** – staff → service | Staff client | `nc -zv -w 5 <SERVER_IP> <PORT>` (or `curl -k https://<SERVER_IP>` for HTTPS) | Connection succeeds ("succeeded/open") | `<paste>` |
| T2 | **BLOCKED** – guest → service | Guest client | `nc -zv -w 5 <SERVER_IP> <PORT>` | Times out / no connection (packet dropped) | `<paste>` |
| T3 | **BLOCKED** – other network → service | Other client | `nc -zv -w 5 <SERVER_IP> <PORT>` | Times out / no connection (packet dropped) | `<paste>` |

Optional extra evidence (recommended):
```bash
sudo iptables -L RECORDS_FILTER -n -v --line-numbers   # packet counters increase on the matching rule
sudo journalctl -k | grep RECORDS-BLOCK                 # or: dmesg | grep RECORDS-BLOCK
```
ACTUAL counters / log lines:
```
<paste here>
```

## Conclusion
`<One or two sentences: did every test behave as expected? Mention any difference and how you fixed it.>`
