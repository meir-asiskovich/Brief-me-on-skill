# Brief-me-on-skill
threat-intel-brief
A Claude skill that produces comprehensive cyber threat intelligence briefings for any domain, threat actor, CVE, product, or vendor — in minutes instead of days.

Built for security analysts, SOC teams, IR leads, and threat researchers. No product roadmap, no Jira audits — pure threat intelligence.


**What it does**
Given any starting point, the skill researches and compiles a structured brief covering:

CVE & Advisory Landscape — vulnerabilities from NVD, CISA KEV, and official agency advisories (CISA, FBI, NSA, NCSC, ENISA, BSI, ANSSI, and more)
Threat Actor Profiles — who's attacking, attribution, motivation, aliases, and MITRE ATT&CK group links
Targeted Organizations & Industries — which sectors and named victims (from public sources only)
Exploitation Techniques — full ATT&CK tactic chain, named malware/tools, detection opportunities
Trend Analysis & Watch List — forward-looking signals for the next 30–90 days


**Input modes**
The skill accepts any of these as a starting point — it detects the type automatically:

**Input type**
Example
CVE ID
CVE-2024-3400 or CVE-2023-46805, CVE-2024-21887
Advisory ID
AA24-131A, NCSC advisory on SVR activity
Free-text description
"that Palo Alto GlobalProtect auth bypass from last month"
Product or vendor
Siemens S7-1500 PLC, VMware vCenter, Hikvision cameras, Juniper Junos
Broad topic
Volt Typhoon, healthcare ransomware, OT/ICS attacks, LockBit 3.0


For product/vendor inputs, the skill starts at the vendor's PSIRT/security advisory page before hitting general CVE databases. OT and IoT devices are routed to ICS-CERT, Dragos, Claroty, and Nozomi in addition to standard sources.


**Example prompts**
Brief me on Volt Typhoon — who they're targeting, what CVEs they use, and which agencies have issued warnings.

CVE-2024-3400 — give me the full threat picture: who's exploiting it, what they do post-exploitation, and who's been targeted.

I saw an alert about a critical FortiOS SSL-VPN vuln from early 2024. Can you pull a threat brief on it?

What's the threat landscape for Siemens S7-1500 PLCs? I need to know known CVEs, who's targeting OT, and any ICS-CERT advisories.

Threat brief on the current ransomware threat to US healthcare — active groups, initial access vectors, TTPs, and official advisories.


**Output**
The skill produces a master briefing document (brief.md + .docx) structured as:

# [Subject] — Cyber Threat Intelligence Brief
## Executive Summary
## 1. CVE & Advisory Landscape
## 2. Threat Actor Profiles
## 3. Targeted Organizations & Industries
## 4. Exploitation Techniques (ATT&CK Mapped)
## 5. Trend Analysis & Watch List
## Appendix: Sources

Each brief includes a BRIEF_HANDOFF.md for continuity — so any analyst or future session can pick up exactly where things left off.


**Sources**
US official agencies
NVD · MITRE CVE · CISA KEV
CISA Advisories · CISA ICS
FBI Cyber / IC3 · NSA Advisories
NIST NCCoE · MITRE ATT&CK · MS-ISAC
EU & UK official agencies
NCSC (UK) · ENISA
CERT-EU · BSI (Germany)
ANSSI (France) · NCSC-NL · CCN-CERT (Spain)
EU CyCLONe
Threat intelligence
Mandiant/Google TAG · CrowdStrike · Microsoft MSTIC · Recorded Future
SecurityWeek · The Hacker News · Krebs on Security
Domain-specific (auto-selected)
OT/ICS: ICS-CERT, Dragos, Claroty, Nozomi Networks
Cloud: Wiz Research, Orca Security, Google Project Zero
Ransomware: BleepingComputer, ransomwatch, ID Ransomware
Nation-state: Recorded Future Insikt Group, ClearSky, Chainalysis (DPRK crypto)


**Installation**
Download threat-intel-brief.skill
In Claude (Cowork or Claude Code), go to Settings → Capabilities → Skills
Click Install skill and select the .skill file

Once installed, trigger it naturally — no command needed. Claude will invoke it automatically when you ask for a threat brief, CVE analysis, or adversary profile.
