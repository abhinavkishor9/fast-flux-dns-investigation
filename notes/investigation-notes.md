# Investigation Notes — Fast-Flux DNS Investigation

## Investigation Focus

The investigation examined whether repeated DNS resolution of the same domain produced changing IP addresses consistent with potential fast-flux behavior.

The primary evidence source was PowerShell DNS resolution. Sysmon Event ID 22 and Wazuh were reviewed for additional endpoint visibility.

---

## Investigation Workspace

The investigation workspace was created at:

```text
C:\FastFluxDNSLab
```

Evidence was stored under:

```text
C:\FastFluxDNSLab\Evidence
```

The evidence directory was successfully created and validated.

---

## Host Baseline

The investigation recorded:

- Hostname: `DESKTOP-9MMM37V`
- PowerShell: 7.6.6
- Windows endpoint: Windows 11 Pro
- Sysmon: Installed
- Wazuh: Available

The host and operating-system information were saved before performing the DNS investigation.

---

## Test Domain

The test-domain variable was set to:

```text
://microsoft.com
```

This was an input-format error in the controlled test.

Despite the malformed value, the resulting DNS-resolution history returned:

```text
23.59.30.63
```

for each observation.

The malformed domain value is documented as part of the investigation because it affects how the test should be interpreted.

---

## DNS Resolution History

Ten DNS-resolution attempts were performed at five-second intervals.

The recorded observations were:

| Time | Domain | IP Address |
|------|--------|------------|
| 05:11:01 | `://microsoft.com` | `23.59.30.63` |
| 05:11:06 | `://microsoft.com` | `23.59.30.63` |
| 05:11:11 | `://microsoft.com` | `23.59.30.63` |
| 05:11:16 | `://microsoft.com` | `23.59.30.63` |
| 05:11:21 | `://microsoft.com` | `23.59.30.63` |
| 05:11:26 | `://microsoft.com` | `23.59.30.63` |
| 05:11:31 | `://microsoft.com` | `23.59.30.63` |
| 05:11:36 | `://microsoft.com` | `23.59.30.63` |
| 05:11:41 | `://microsoft.com` | `23.59.30.63` |
| 05:11:46 | `://microsoft.com` | `23.59.30.63` |

---

## IP Diversity

The unique-IP calculation returned:

```text
1
```

The only observed address was:

```text
23.59.30.63
```

Therefore, the test did not demonstrate IP rotation.

---

## Fast-Flux Assessment

A fast-flux investigation normally looks for evidence such as:

- One domain resolving to multiple IP addresses.
- Frequent changes between addresses.
- Short-lived infrastructure.
- High IP diversity.
- Repeated DNS queries.
- Consistent or suspicious rotation patterns.
- Process-level correlation.
- Additional infrastructure indicators.

The current investigation demonstrated only repeated resolution to the same IP address.

Therefore, the evidence does not establish fast-flux behavior.

---

## Sysmon Event ID 22

Sysmon Event ID 22 was queried using the test-domain value.

The query returned no matching events.

This indicates that the tested DNS activity was not available through the current Sysmon Event ID 22 telemetry query.

Possible explanations include:

- DNS Query events are not being generated.
- Sysmon Event ID 22 is not enabled in the current configuration.
- The query did not match the actual recorded query name.
- The malformed test-domain value affected the search.

This is documented as a telemetry limitation.

---

## Wazuh Investigation

Wazuh data was reviewed to determine whether endpoint DNS telemetry was available.

A document was observed with:

```text
agent.id: 000
agent.name: localhost.localdomain
```

The event contained Wazuh-manager network/listening-port information.

Because this was agent `000` and the manager host rather than the Windows endpoint `DESKTOP-9MMM37V`, it was not treated as evidence of DNS activity from the investigated workstation.

This distinction is important when correlating Wazuh data.

---

## Evidence Files

The following files were created:

```text
Host-Role.txt
Operating-System.txt
Investigation-Time.txt
DNS-Resolution-History.csv
Investigation-Summary.txt
```

---

## Final Assessment

The investigation did not establish fast-flux DNS behavior.

The repeated DNS-resolution test returned one unique IP address:

```text
23.59.30.63
```

No IP rotation was observed across the ten recorded resolutions.

Sysmon Event ID 22 did not provide matching DNS telemetry, and the Wazuh event reviewed belonged to the Wazuh manager rather than the investigated Windows endpoint.

The appropriate conclusion is:

**No fast-flux behavior was demonstrated; DNS telemetry visibility and the malformed test-domain input are documented limitations.**
