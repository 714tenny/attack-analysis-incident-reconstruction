# Analyst filters and artifact hashing

The completed report used Wireshark filters, TCP stream following, Event Viewer, and PowerShell hashing. These examples document an equivalent review workflow; the original PCAP is not included.

| Wireshark display filter | Purpose |
| --- | --- |
| `ip.addr == 192.168.134.129 && ip.addr == 192.168.134.132` | Scope traffic between the two principal systems |
| `tcp.port == 445` | Review SMB transport traffic |
| `tcp.stream == 1` | Examine the victim callback and transfer |
| `tcp.stream == 154` | Examine the later repeated SMB exchange |
| `ip.src == 192.168.134.129 && ip.dst == 192.168.134.132 && tcp.flags.syn == 1 && tcp.flags.ack == 0` | Inspect initial SYN attempts |

In Follow TCP Stream, select the source-to-target direction before exporting raw bytes. Hash the resulting export without executing it:

```powershell
Get-FileHash -Algorithm SHA256 -LiteralPath .\stream1_129_to_132.raw
```

These review filters are documented examples, not tested detection rules or production alerts.
