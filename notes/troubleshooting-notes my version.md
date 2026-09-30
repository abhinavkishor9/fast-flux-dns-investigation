# Troubleshooting Notes — Fast-Flux DNS Investigation

## 1. Test Domain Input Issue

The test-domain variable was entered as:

```text
://microsoft.com
```

instead of a normal hostname such as:

```text
www.microsoft.com
```

This was an input-format issue during the controlled test.

The value was retained in the investigation notes because it was part of the actual execution history.

For a future rerun, define the domain as:

```powershell
$TestDomain = "www.microsoft.com"
```

---

## 2. Only One Unique IP Was Observed

The unique-IP calculation returned:

```text
1
```

The only observed address was:

```text
23.59.30.63
```

This means the controlled test did not demonstrate DNS IP rotation.

This is not a failure of the investigation. It is a valid observation.

A fast-flux hypothesis requires evidence of changing infrastructure, so the absence of IP rotation must be documented.

---

## 3. Sysmon Event ID 22 Returned No Matching Events

The following query produced no matching result:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = "Microsoft-Windows-Sysmon/Operational"
    Id = 22
} -MaxEvents 500 |
Where-Object {
    $_.Message -match [regex]::Escape($TestDomain)
} |
Select-Object TimeCreated, Id, Message
```

Possible causes include:

- Event ID 22 is not enabled in the Sysmon configuration.
- Sysmon is not recording DNS Query events.
- The query name does not match the value being searched.
- The malformed test-domain value affected the search.

The absence of results was therefore treated as a telemetry limitation.

---

## 4. Wazuh Result Was From Agent 000

A Wazuh document was observed with:

```text
agent.id: 000
agent.name: localhost.localdomain
```

This represents the Wazuh manager rather than the Windows endpoint being investigated.

The event showed manager-side network/listening-port information.

It was therefore not used as evidence of DNS activity on:

```text
DESKTOP-9MMM37V
```

---

## 5. Correct Wazuh Starting Point

For future investigation, first verify endpoint telemetry with:

```text
agent.id:"001" AND agent.name:"DESKTOP-9MMM37V"
```

Then check for Sysmon events:

```text
agent.id:"001" AND agent.name:"DESKTOP-9MMM37V" AND data.win.system.eventID:"22"
```

If this produces no results, inspect whether other Sysmon events are arriving before concluding that DNS telemetry is unavailable.

---

## 6. Fast-Flux Does Not Mean Any Multiple-IP Result

Even if a future test returns multiple IP addresses, that alone should not be classified as fast-flux behavior.

Legitimate services may use:

- CDNs
- Cloud infrastructure
- Load balancing
- Geographic DNS
- Anycast
- Traffic management

The investigation therefore needs timing, IP diversity, TTL information where available, process context, and infrastructure context.

---

