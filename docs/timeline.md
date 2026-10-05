# Reconstructed incident timeline

Times are preserved as displayed in the completed report; the source evidence does not establish a UTC conversion.

| July 8, 2017 time | Event | Assessment |
| --- | --- | --- |
| 12:37:51 | Terminal Services start control | Service availability; RDP attack use unproven |
| 13:56:46 | Tcpip event 4226 | Earlier connection-limit warning; cause unproven |
| 14:09:32 | Capture begins | Start of available packet evidence |
| 14:09:40.507590 | Source connects to target TCP 445 | First observed SMB attack sequence |
| Approximately 14:09:40.533 | Blank-credential session succeeds; IPC$ accessed | Null-session access confirmed |
| 14:09:40.555789 | Suspicious Server Service RPC request | Exploit-like activity |
| 14:09:40.733284 | Target initiates TCP 29922 callback | Approximately 0.177495 seconds after RPC request |
| Following callback | Approximately 1.63 MB of PE-containing data sent to target | Transfer confirmed; execution unproven |
| Approximately 14:13:23 | 154 initial SYN attempts across multiple ports | Later reconnaissance |
| Approximately 14:13:26 | Another SMB/RPC exchange | Repeated attack pattern |
| 14:13:45 | Capture ends | Later activity unknown |
