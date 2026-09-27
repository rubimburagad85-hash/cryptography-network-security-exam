# Firewall test evidence (`filter_tests.md`)
Markdown# Firewall Test Evidence (filter_tests.md)
## Lab topology
| Item | Value |
| :--- | :--- |
| **Records server IP** | `192.168.100.10` |
| **Service tested (given by assessor)** | `tcp/443` |
| **Staff client (permitted)** | `192.168.10.50` in `192.168.10.0/24` |
| **Guest client (blocked)** | `10.0.99.15` in `10.0.99.0/24` |
| **Other/external client (blocked)** | `203.0.113.45` |
| **Firewall tool** | `iptables` (script: `firewall/records_server_firewall.sh`) |
## Applying the rules (on the server / gateway)
```bash
sudo ./firewall/records_server_firewall.sh apply
sudo ./firewall/records_server_firewall.sh show
ACTUAL show output:PlaintextChain RECORDS_FILTER (2 references)
num  target     prot opt source               destination         
1    ACCEPT     tcp  --  192.168.10.0/24      192.168.100.10      tcp dpt:443
2    DROP       tcp  --  10.0.99.0/24         192.168.100.10      tcp dpt:443
3    DROP       tcp  --  0.0.0.0/0            192.168.100.10      tcp dpt:443
TestsTestRun onCommandExpected outcomeActual resultT1PERMITTED – staff → serviceStaff client:nc -zv -w 5 192.168.100.10 443 | Connection succeeds ("succeeded/open") | Connection to 192.168.100.10 443 port [tcp/https] succeeded! || T2 | BLOCKED – guest → service | Guest client:nc -zv -w 5 192.168.100.10 443 | Times out / no connection (packet dropped) | nc: connect to 192.168.100.10 port 443 (tcp) timed out || T3 | BLOCKED – other network → service | Other client:nc -zv -w 5 192.168.100.10 443 | Times out / no connection (packet dropped) | nc: connect to 192.168.100.10 port 443 (tcp) timed out |Optional extra evidenceBashsudo iptables -L RECORDS_FILTER -n -v --line-numbers
ACTUAL counters / log lines:Plaintextnum   pkts bytes target     prot opt in     out     source               destination         
1        5   300 ACCEPT     tcp  --  *      *       192.168.10.0/24      192.168.100.10      tcp dpt:443
2       12   720 DROP       tcp  --  *      *       10.0.99.0/24         192.168.100.10      tcp dpt:443
3        3   180 DROP       tcp  --  *      *       0.0.0.0/0            192.168.100.10      tcp dpt:443
## Conclusion
Every test behaved exactly as expected during the laboratory execution. Authorized staff successfully established a secure connection to the records server on port 443, while traffic originating from both the guest network and external hosts was successfully blocked and dropped by the firewall. No discrepancies were observed.

