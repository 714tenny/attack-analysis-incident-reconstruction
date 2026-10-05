# Attack Analysis & Incident Reconstruction — Investigation Notes

## Evidence and scope

The completed report examined `Maybe Hacked Box--Capture.pcapng`, `Maybe Hacked Box--Application Log.evt`, and `Maybe Hacked Box--Security Log.evt`. The capture contained 2,364 packets and 1,921,245 captured bytes over 252.934 seconds, approximately 14:09:32–14:13:45 on July 8, 2017.

| System | Recorded role |
| --- | --- |
| `192.168.134.129` | Suspected attack/control host |
| `192.168.134.132` | Target host, `APLOVERS-765952` |
| `192.168.134.158`, `192.168.134.2` | Additional local infrastructure/name-resolution traffic |

The event-log review identified Windows XP SP3. No disk or memory image was available. Findings below summarize the original completed investigation; the raw PCAP and exported PE are not hosted here.

## Sequence and assessment

1. The source connected to target TCP 445 and negotiated SMB.
2. An initial NTLM attempt failed. A later Session Setup supplied blank account/domain and zero-length password fields; packet 39 returned success.
3. Packet 41 confirmed successful `\\192.168.134.132\IPC$` access.
4. An attempt to open `SRVSVC` was denied, followed by successful `BROWSER` named-pipe access and DCE/RPC communication.
5. Packet 52 contained `NETRPRNAMECANONICALIZE`, operation 34. Packet 55 returned `nca_s_fault_ndr`.
6. The target connected from port 1241 to the suspected source on TCP 29922 about 0.177 seconds after the suspicious RPC request.
7. The source sent approximately 1,630 kB containing Windows PE signatures to the target.
8. Near the end of the capture, 154 initial SYN attempts across multiple ports were followed by another SMB/RPC sequence.

The sequence is consistent with an MS08-067-style Server Service exploitation attempt. The rapid callback strongly supports probable attacker-controlled execution, but neither a specific CVE nor the subsequent PE's execution is independently confirmed by these files. The later scan must not be moved ahead of the first SMB exchange in the timeline.

## Payload preservation

The investigator exported only the source-to-target direction of TCP stream 1:

`192.168.134.129:29922 → 192.168.134.132:1241`

The exported artifact was saved as `stream1_129_to_132.raw`, without execution. Reassembled data showed `MZ`, `PE`, the DOS-mode string, and sections including `.text`, `.rdata`, `.data`, and `.reloc`.

SHA-256 recorded in the submitted report:

```text
1F60D8F0A43129657EA9A3B83DC7649F10DC1EF55A040BE9E9B35D34B7EEDD4F
```

This hash identifies the exported transfer bytes. It has not been recomputed in this publication because the raw artifact is not available here. A PE signature does not establish a malware family, original filename, signer, or successful installation.

## Host-log correlation

- Service Control Manager 7035/7036 indicated Terminal Services start activity at 12:37:51. This does not prove RDP use by the attacker.
- Tcpip 4226 at 13:56:46 indicated excessive concurrent connection attempts before the capture. It cannot be directly attributed to the later inbound SYN scan.
- The file named Security Log contained System-style entries rather than a complete normal logon-auditing history. File names alone are not sufficient to classify evidence.
- The Application log did not expose an obvious malicious installation. Missing log evidence is not proof of a clean host.

## Response recommendations

Preserve the packet/log evidence and raw export, contain the target and investigate the source, hunt for the artifact hash, examine memory/disk and persistence, restrict unauthorized SMB access and anonymous sessions, replace the legacy host, and centralize reliable audit logs. These are recommendations; this report did not validate remediation.

## Limits

The four-minute capture, incomplete auditing, and missing disk/memory evidence leave payload execution, privilege level, persistence, credential theft, lateral movement, data exfiltration, and full incident scope unconfirmed.
