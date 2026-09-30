# Fast-Flux DNS Investigation

## Overview

This lab investigates the DNS characteristics associated with potential fast-flux infrastructure.

Fast-flux DNS is commonly associated with infrastructure where a domain resolves to changing IP addresses over time. From a DFIR perspective, the investigation should therefore examine DNS resolution history, IP diversity, timing, process context, and available endpoint telemetry.

The lab uses repeated PowerShell DNS resolution together with Sysmon Event ID 22 and Wazuh telemetry where available.

The investigation was intentionally performed without contacting known malicious infrastructure.

---

## Lab Objectives

- Perform repeated DNS resolution for a selected domain.
- Record the returned IP addresses over time.
- Calculate the number of unique IP addresses observed.
- Determine whether IP rotation occurred during the observation window.
- Review Sysmon Event ID 22 DNS telemetry.
- Review Wazuh telemetry for supporting endpoint evidence.
- Document telemetry limitations.
- Distinguish normal DNS behavior from potential fast-flux characteristics.

---

## Environment

- Operating System: Windows 11 Pro
- Host: DESKTOP-9MMM37V
- PowerShell: 7.6.6
- Sysmon: Installed
- Wazuh Agent: 001
- Primary telemetry:
  - PowerShell DNS resolution
  - Sysmon Event ID 22
  - Wazuh endpoint telemetry

---

## Lab Scenario

A security analyst is investigating whether repeated DNS resolution for a domain demonstrates characteristics that could be associated with fast-flux infrastructure.

The investigation collects multiple DNS resolutions over a short period and compares the returned IP addresses.

The expected investigative question is:

> Does the same domain resolve to multiple IP addresses within the observation period?

If multiple addresses are observed, the analyst must determine whether the behavior is consistent with legitimate DNS infrastructure such as CDNs, cloud services, or load balancing, or whether additional evidence supports a fast-flux hypothesis.

---

## Investigation Workflow

1. Create the investigation workspace.
2. Record the host and operating-system baseline.
3. Define the test domain.
4. Perform repeated DNS resolution.
5. Save the DNS-resolution history.
6. Extract unique IP addresses.
7. Review Sysmon Event ID 22.
8. Review Wazuh telemetry.
9. Compare the observed DNS behavior with the expected fast-flux pattern.
10. Document limitations and the final assessment.

---

## DNS Resolution Results

Ten DNS resolution attempts were performed during the investigation.

The observed result was:

| Observation | Domain | IP Address |
|-------------|--------|------------|
| 1 | `://microsoft.com` | `23.59.30.63` |
| 2 | `://microsoft.com` | `23.59.30.63` |
| 3 | `://microsoft.com` | `23.59.30.63` |
| 4 | `://microsoft.com` | `23.59.30.63` |
| 5 | `://microsoft.com` | `23.59.30.63` |
| 6 | `://microsoft.com` | `23.59.30.63` |
| 7 | `://microsoft.com` | `23.59.30.63` |
| 8 | `://microsoft.com` | `23.59.30.63` |
| 9 | `://microsoft.com` | `23.59.30.63` |
| 10 | `://microsoft.com` | `23.59.30.63` |

The same IP address was returned for all ten observations.

### Unique IP Count

```text
1
```

Only one unique IP address was observed.

---

## Sysmon Findings

Sysmon Event ID 22 was queried for the test domain.

The query returned no matching events.

This means that DNS Query telemetry for the tested domain was not available through the current Sysmon Event ID 22 collection.

This should be treated as a telemetry limitation rather than evidence that no DNS activity occurred.

---

## Wazuh Findings

Wazuh data was reviewed during the investigation.

A Wazuh document was observed for:

```text
agent.id: 000
agent.name: localhost.localdomain
```

The event contained Wazuh-manager network/listening-port information.

This was not endpoint telemetry from `DESKTOP-9MMM37V` and therefore was not used as evidence of fast-flux DNS activity on the Windows workstation.

---

## Assessment

The investigation did **not** demonstrate fast-flux behavior.

The tested domain returned a single IP address, `23.59.30.63`, across all ten observations. No IP rotation was observed during the test window.

The available Sysmon Event ID 22 query also produced no matching DNS events, limiting endpoint DNS telemetry.

Therefore:

**Fast-flux behavior was not established from the collected evidence.**

The investigation demonstrates the correct analytical approach: IP diversity and DNS rotation must be observed and correlated before making a fast-flux assessment.

---

## Evidence

The investigation workspace was:

```text
C:\FastFluxDNSLab\Evidence
```

Evidence generated during the lab includes:

- `Host-Role.txt`
- `Operating-System.txt`
- `Investigation-Time.txt`
- `DNS-Resolution-History.csv`
- `Investigation-Summary.txt`

---

## DFIR Lesson

Multiple IP addresses for a domain do not automatically indicate fast-flux DNS.

A stronger investigation should establish:

```text
Same Domain
     ↓
Multiple IP Addresses
     ↓
Repeated Changes
     ↓
Short/Unusual Rotation Pattern
     ↓
Process + DNS Correlation
     ↓
Infrastructure Context
     ↓
Assessment
```

In this investigation, the required IP-rotation pattern was not observed.
