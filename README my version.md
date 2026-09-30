# fast-flux-dns-investigation
## Overview
Fast-flux DNS is a technique where a domain rapidly changes its DNS records, especially IP addresses, to make malicious infrastructure harder to identify, block, or take down.

In a SOC/DFIR investigation, the important pattern is:

Same domain
    ↓
Multiple destination IPs
    ↓
Short time intervals / frequent changes
    ↓
Potential infrastructure rotation
    ↓
Investigate for fast-flux behavior

A single domain resolving to multiple IPs is not automatically malicious. CDNs, cloud services, load balancers, and legitimate applications can also use multiple IP addresses.

This lab investigates the DNS characteristics associated with potential fast-flux infrastructure.

Fast-flux DNS is commonly associated with infrastructure where a domain resolves to changing IP addresses over time. From a DFIR perspective, the investigation should therefore examine DNS resolution history, IP diversity, timing, process context, and available endpoint telemetry.

The lab uses repeated PowerShell DNS resolution together with Sysmon Event ID 22 and Wazuh telemetry where available.

The investigation was intentionally performed without contacting known malicious infrastructure.

---

## Lab Objectives

- Establish a controlled environment for examining DNS infrastructure behavior.
- Perform repeated DNS resolution attempts against a selected domain and record the returned DNS responses.
- Compare DNS responses collected at different points in time to identify changes in destination IP addresses.
- Calculate IP-address diversity and determine whether the observed domain demonstrates infrastructure rotation.
- Examine the relationship between DNS query frequency, timestamps, and returned addresses.
- Review Sysmon DNS telemetry to determine whether DNS Query events are available for endpoint-level correlation.
- Investigate Wazuh telemetry and distinguish endpoint events from manager-generated events.
- Identify limitations in DNS, Sysmon, and Wazuh visibility that may affect the investigation.
- Differentiate potential fast-flux characteristics from legitimate DNS behavior such as CDN distribution, cloud hosting, and load balancing.
- Apply an evidence-based assessment without treating multiple DNS responses or repeated queries as automatic indicators of malicious activity.
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

A Windows endpoint is being investigated for DNS behavior that could potentially resemble infrastructure rotation. The objective is to examine repeated DNS resolutions over a short observation period and determine whether the domain returns changing IP addresses over time.

The investigation is performed using **PowerShell**, with additional validation through **Sysmon** and **Wazuh** where telemetry is available. The investigation focuses on observable DNS behavior rather than assuming that changing DNS responses are malicious.

The analyst will:

- Establish an evidence workspace and record the host and investigation details.
- Perform repeated A-record DNS resolutions at regular intervals.
- Record the returned IP addresses and timestamps for each observation.
- Calculate the number of unique IP addresses returned during the test.
- Review Sysmon Event ID 22 for endpoint DNS query telemetry.
- Examine Wazuh Discover results and verify whether the events belong to the investigated Windows endpoint.
- Distinguish endpoint telemetry from unrelated Wazuh manager events.
- Assess whether the observed DNS pattern provides evidence of infrastructure rotation.

The investigation must also account for legitimate reasons why a domain may resolve to different addresses, including CDNs, cloud infrastructure, geographic distribution, and load balancing. A single IP address, multiple IP addresses, or repeated DNS queries should therefore not be treated as proof of fast-flux behavior without additional supporting evidence.

The final assessment will document the observed DNS pattern, available telemetry, investigation limitations, and whether the collected evidence supports or does not support the fast-flux hypothesis.

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

