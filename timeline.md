# Timeline — Fast-Flux DNS Investigation

| Time | Source | Event | Relevance |
|------|--------|-------|-----------|
| 05:06 | PowerShell | `C:\FastFluxDNSLab\Evidence` created | Investigation workspace |
| 05:06+ | PowerShell | Host and OS baseline collected | Environment identification |
| 05:11:01 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:06 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:11 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:16 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:21 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:26 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:31 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:36 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:41 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| 05:11:46 | PowerShell DNS | DNS resolution | `23.59.30.63` returned |
| After DNS test | PowerShell | Unique IP calculation | One unique IP identified |
| After DNS test | Sysmon | Event ID 22 query | No matching event returned |
| Investigation period | Wazuh | Manager telemetry reviewed | Agent 000 / localhost observed |

## DNS Timeline Summary

The same IP address was returned during all ten DNS observations:

```text
23.59.30.63
```

No IP address rotation was observed.

## Investigation Sequence

1. Created the Fast-Flux DNS investigation workspace.
2. Collected host and operating-system information.
3. Defined the test-domain variable.
4. Performed ten DNS-resolution attempts at five-second intervals.
5. Saved the DNS-resolution history to CSV.
6. Extracted unique IP addresses.
7. Determined that only one unique IP was observed.
8. Queried Sysmon Event ID 22 for DNS telemetry.
9. Found no matching Sysmon DNS event.
10. Reviewed Wazuh telemetry.
11. Identified that the available Wazuh document belonged to agent `000`.
12. Documented the telemetry and input limitations.
13. Concluded that fast-flux behavior was not demonstrated.

## Final Assessment

The timeline shows repeated DNS resolution without observed IP rotation.

The collected evidence therefore does not establish fast-flux DNS behavior.

The investigation also identified two important limitations:

- The test-domain value was malformed.
- Sysmon Event ID 22 did not provide matching DNS telemetry.

These limitations are preserved as part of the investigation rather than being treated as evidence of malicious or benign activity.
