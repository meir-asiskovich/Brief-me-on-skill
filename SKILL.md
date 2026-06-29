---
name: threat-intel-brief
description: >
  Produces a comprehensive cyber threat intelligence briefing for any domain, threat actor, CVE,
  or product — in minutes instead of days. Use whenever a security analyst, SOC team, or IR lead
  needs to understand who is attacking, what they're exploiting, who they're targeting, and where
  the threat is heading. Accepts any starting point: a CVE ID (e.g. CVE-2024-3400), an advisory
  ID (e.g. AA24-131A), free-text description (e.g. "that Palo Alto SSL-VPN RCE"), a product or
  vendor name (e.g. "Siemens S7-1500", "VMware vCenter", "Hikvision cameras", "Juniper Junos"),
  or a broad topic (Volt Typhoon, healthcare ransomware, OT/ICS). Trigger on: "threat brief on
  X", "who's exploiting X", "what APTs target X", "advisories for X", "what CVEs affect X", or
  any request to get up to speed on adversaries, CVEs, TTPs, and active campaigns. Use
  proactively — if someone is starting an IR or threat hunt, suggest it.
---

# Cyber Threat Intelligence Brief Skill

This skill produces a comprehensive threat intelligence briefing for any technology domain,
adversary group, or attack campaign. It turns what used to take days of analyst work into a
ready-to-share document in minutes.

The briefing covers:
1. CVE & advisory landscape from official agencies
2. Threat actor profiles (who is attacking and how)
3. Targeted organizations and industries
4. Exploitation techniques and MITRE ATT&CK mapping
5. Trend analysis and forward-looking watch list

The output is a single structured document aimed at security analysts, SOC teams, IR leads,
and threat researchers — not product managers. There are no roadmap recommendations, Jira
audits, or product gap analyses.

---

## User Inputs

Collect the following at the start. The research subject is required; everything else is optional.

**Required:**

**Research subject** — Accept any of the following as a starting point. The skill should
recognize the input type automatically and pivot its research accordingly:

- **Free-text description**: A natural language description of a vulnerability, attack, or threat.
  e.g., "that Palo Alto GlobalProtect auth bypass that came out last month" or "the MOVEit
  supply chain attack" → resolve to specific CVEs and advisories, then proceed.

- **CVE ID**: One or more CVE numbers.
  e.g., "CVE-2024-3400" or "CVE-2023-46805, CVE-2024-21887" → look up the CVE(s) on NVD and
  MITRE, identify affected products, find related advisories, then expand outward to attribution,
  targeting, and TTPs.

- **Advisory ID**: A CISA, FBI, NSA, NCSC, BSI, ANSSI, or other agency advisory reference.
  e.g., "AA24-131A" or "NCSC advisory on SVR activity" → fetch the full advisory, extract all
  CVEs and named actors referenced, then expand into a full brief.

- **Product name or vendor**: Any specific software product, operating system, OT device, or
  IoT device — regardless of whether a CVE or advisory is known.
  e.g., "Siemens S7-1500 PLC", "Juniper Junos", "Hikvision cameras", "VMware vCenter",
  "Schneider Electric EcoStruxure", "Android 14", "Cisco IOS XE", "AXIS network cameras" →
  go to the vendor's official security advisory page first, then cross-reference NVD, CISA KEV,
  and ICS-CERT (for OT/IoT). Build the CVE list from there, then expand to attribution and TTPs.
  For OT/IoT devices, also check ICS-CERT, Dragos, Claroty, and Nozomi advisories.
  The key distinction from free-text: the user knows exactly what product they care about,
  so start from the vendor's own security bulletins before branching out.

- **Technology area, threat actor, or sector** (broad mode): An APT group, sector, or campaign
  where no single product or CVE is the anchor.
  e.g., Volt Typhoon, healthcare ransomware, OT/ICS attacks, LockBit 3.0, Chinese APT activity.

If the user's input could match multiple modes (e.g., "Fortinet" could be a vendor or a topic),
default to the **product/vendor** path — go to Fortinet's PSIRT page first — and note at the
top of the brief which interpretation was used.

**Optional:**

**1. Time window**
How far back should the research go? Default: last 12 months. Adjust for emerging campaigns
(last 30 days) or historical context (3–5 years).

**2. Geographic or sector focus**
Should the brief focus on a specific region (e.g., US critical infrastructure, EU financial
sector) or industry vertical (e.g., energy, healthcare, defense)?

**3. Specific threat actors to include or exclude**
If the user already knows which APTs or groups are relevant, include them. If there are known
irrelevant actors, exclude them to keep the brief focused.

**4. Additional sources**
Any specific URLs, vendor advisories, or researcher blogs to include beyond the standard source
list. Add these to the research queue.

**5. Output format**
- **Google Docs** — create and share via Google Drive connector
- **Confluence** — publish to a Confluence space (ask for space key or parent page URL)
- **Local files** (default) — save `.md` and `.docx` files to the working folder

If not specified, default to **local files** (`.md` + `.docx`).

### Table formatting rule
All tables must be real grid tables — never ASCII pipe-character tables or space-aligned columns.
Use the `docx` skill to generate properly formatted `.docx` files with real tables.

---

## Phase 1: CVE & Advisory Research

**Goal:** Build a comprehensive picture of known vulnerabilities and official warnings covering
the research subject.

### Step 1: Resolve input and define scope

The starting point determines the research path. Handle each input type as follows:

**If input is a CVE ID** (e.g., CVE-2024-3400):
1. Look up the CVE on NVD (https://nvd.nist.gov/vuln/detail/CVE-XXXX-XXXXX) and MITRE
2. Extract: affected product(s), vendor, vulnerability class, CVSS, references
3. Check CISA KEV — is it listed?
4. Search CISA advisories for any that reference this CVE
5. Use the affected product as the research subject for Phases 2–5
6. Note related CVEs in the same product/campaign (e.g., chained exploits)

**If input is an advisory ID** (e.g., AA24-131A, NCSC-2024-001):
1. Fetch the full advisory from the issuing agency's website
2. Extract: all CVEs listed, named threat actors, targeted sectors, recommended mitigations
3. Use the CVEs and actors as the seed for Phases 2–5
4. Cross-reference other advisories that cite the same actors or CVEs

**If input is free-text description** (e.g., "the Palo Alto GlobalProtect auth bypass"):
1. Resolve the description to specific CVE(s) — search NVD, CISA KEV, and recent security
   news to identify the most likely match
2. Confirm the resolved CVE(s) with a brief note before proceeding (e.g., "This appears to be
   CVE-2024-3400 — Palo Alto Networks PAN-OS GlobalProtect, CVSS 10.0")
3. Then follow the CVE path above

**If input is a product name or vendor** (e.g., "Siemens S7-1500", "VMware vCenter", "Hikvision"):
1. Go to the vendor's official security advisory / PSIRT page first:
   - Software vendors: e.g., https://www.vmware.com/security/advisories, https://sec.cloudapps.cisco.com/security/center/publicationListing.x
   - OT vendors: Siemens ProductCERT (https://www.siemens.com/cert), Schneider Electric (https://www.se.com/ww/en/work/support/cybersecurity/security-notifications.jsp), Rockwell Automation (https://www.rockwellautomation.com/en-us/support/advisory.html)
   - IoT/camera vendors: Hikvision (https://www.hikvision.com/en/support/cybersecurity/security-advisory/), Axis (https://www.axis.com/support/cybersecurity/security-advisories)
   - For any vendor not listed, search "[vendor name] PSIRT" or "[vendor name] security advisory"
2. Cross-reference all found CVEs against NVD and CISA KEV
3. For OT/IoT products, also check ICS-CERT (https://www.cisa.gov/ics-advisories), Dragos, Claroty, and Nozomi Networks advisories
4. Note the product's deployment footprint — how widely used is it, and in what sectors?
5. Use the product's CVE list as the seed for Phases 2–5

**If input is a technology area, actor, or sector** (broad mode):
Break the subject into logical sub-areas. Examples:
- **OT/ICS broadly**: PLCs (Siemens, Rockwell), HMIs, historian software, protocol stacks (Modbus, DNP3, EtherNet/IP)
- **Ransomware group (e.g., Cl0p)**: Exploited CVEs across MOVEit, GoAnywhere, Accellion
- **Nation-state campaign**: Anchor on the actor, then enumerate their known CVE exploitation history

### Step 2: Pull CVEs from official sources

For each product or sub-area, search:

1. **NVD** (https://nvd.nist.gov) — primary CVE database
2. **MITRE CVE** (https://cve.mitre.org) — cross-reference IDs
3. **CISA KEV** (https://www.cisa.gov/known-exploited-vulnerabilities-catalog) — known-exploited
   status is critical signal
4. **CISA ICS Advisories** (https://www.cisa.gov/ics-advisories) — for OT/ICS/IoT subjects
5. **NIST NCCoE** (https://www.nccoe.nist.gov) — sector-specific practice guides; NVD is
   the primary CVE database but NCCoE guides provide implementation and mitigation context
6. **GitHub Security Advisories** (https://github.com/advisories) — for software libraries
   and open-source tools

For each CVE, capture:
- CVE ID and link
- CVSS score and severity (Critical ≥9.0, High 7.0–8.9, Medium 4.0–6.9)
- Short description of the vulnerability class (auth bypass, RCE, LPE, etc.)
- Exploitation status: CISA KEV, actively exploited in the wild, PoC available, or patched only
- Attributed threat actor(s) if known
- Recency: Recent (≤6 months) or Older

### Step 3: Pull official advisories

Advisories give context CVE databases don't: attribution, targeting, and remediation urgency.
Always check:

- **CISA Alerts & Advisories**: https://www.cisa.gov/news-events/cybersecurity-advisories
- **FBI Cyber Division**: https://www.ic3.gov / https://www.fbi.gov/investigate/cyber
- **FBI–CISA Joint Advisories** (search "AA2X-XXXXX" format)
- **NSA Cybersecurity Advisories**: https://www.nsa.gov/Press-Room/Cybersecurity-Advisories-Guidance/
- **NCSC (UK)**: https://www.ncsc.gov.uk/section/keep-up-to-date/alerts-advisories
- **ENISA** (EU threat landscape): https://www.enisa.europa.eu/publications/enisa-threat-landscape
- **CERT-EU** (EU institutions): https://cert.europa.eu/publications/security-advisories
- **BSI (Germany)**: https://www.bsi.bund.de/EN — strong on industrial/critical infra advisories
- **ANSSI (France)**: https://www.cert.ssi.gouv.fr — high-quality nation-state threat reporting
- **NCSC-NL (Netherlands)**: https://www.ncsc.nl/english — strong on attribution and advisories
- **CCN-CERT (Spain)**: https://www.ccn-cert.cni.es/en
- **EU CyCLONe / ENISA CSIRT Network**: Cross-border incident coordination and joint advisories
- **MS-ISAC** (for state/local gov): https://www.cisecurity.org/ms-isac/advisories

For each advisory, capture:
- Advisory ID and title
- Issuing agency and date
- Subject: what product/sector/actor it covers
- Key CVEs referenced
- Attributed actor or campaign (if named)
- Recommended actions/IOCs provided

### Output: `[Subject]_CVE_Advisory_Report.md`

```
# [Subject] — CVE & Advisory Report
## Summary Statistics
- Total CVEs: X | Critical: X | High: X | Medium: X
- CISA KEV entries: X
- Official advisories reviewed: X

## CVEs by Sub-Area
| CVE | CVSS | Severity | Vulnerability Class | Status | Attributed Actor |
|-----|------|----------|---------------------|--------|-----------------|

## Official Advisories
| Advisory ID | Agency | Date | Subject | CVEs Referenced | Actor Named |
|-------------|--------|------|---------|-----------------|-------------|
```

---

## Phase 2: Threat Actor Profiles

**Goal:** Identify and profile the adversaries behind attacks in this domain — who they are,
where they operate from, and what their goals are.

### Sources for actor intelligence

- **MITRE ATT&CK Groups**: https://attack.mitre.org/groups/ — the canonical reference
- **CISA advisories** — often name actors explicitly in AA-series advisories
- **Mandiant/Google TAG threat actor library**: https://www.mandiant.com/resources/blog
- **CrowdStrike Adversary Universe**: https://www.crowdstrike.com/adversaries/
- **Microsoft MSTIC**: https://www.microsoft.com/en-us/security/blog/topic/threat-intelligence/
- **Recorded Future**: https://www.recordedfuture.com/research
- **CISA's China, Russia, Iran, DPRK-specific advisories** (nation-state focus)

### For each threat actor, document

- **Name(s)**: Primary name and known aliases (e.g., APT40 / BRONZE MOHAWK / TEMP.Periscope)
- **Attribution**: Country/origin if assessed (confidence level where stated)
- **Motivation**: Espionage, financial, sabotage, hacktivism
- **Active since**: Approximate first observed date
- **Recent campaigns**: Active operations relevant to the research subject
- **Preferred initial access vectors**: Phishing, exploitation of public-facing apps, supply chain, etc.
- **Known CVEs exploited**: Cross-reference from Phase 1
- **ATT&CK Groups page link**: Direct link to their MITRE ATT&CK entry

### Output: `[Subject]_Threat_Actor_Profiles.md`

```
# [Subject] — Threat Actor Profiles

## Actor: [Name]
**Aliases:** [list]
**Attribution:** [country, confidence]
**Motivation:** [espionage / financial / sabotage]
**Active since:** [year]
**Relevant campaigns:** [brief description]
**Preferred techniques:** [summary]
**CVEs exploited (from this research):** [CVE-XXXX-XXXXX, ...]
**ATT&CK reference:** [link]
```

---

## Phase 3: Targeted Organizations & Industries

**Goal:** Map which types of organizations and sectors are being targeted, and document
specific cases where named organizations have been compromised.

### What to document

- **Primary targeted sectors**: e.g., Energy, Healthcare, Financial Services, Defense,
  Government, Telecoms, Manufacturing
- **Geographic concentration**: Are attacks focused on a specific country or region?
- **Named victim organizations**: From official advisories, court filings, breach disclosures,
  or credible news sources — never speculate
- **Why this sector**: What makes this sector attractive (operational data, financial assets,
  critical infrastructure disruption, IP theft)
- **Supply chain risk**: Are attacks targeting a sector's software/hardware suppliers rather
  than the sector directly?

### Sources for targeting intelligence

- CISA advisories often state targeted sectors explicitly
- FBI indictments and DOJ press releases name victims
- HHS (for healthcare): https://www.hhs.gov/hipaa/for-professionals/breach-notification/index.html
- FS-ISAC (for financial sector): https://www.fsisac.com
- H-ISAC, E-ISAC, WaterISAC for sector-specific feeds
- Credible security vendor reports: Mandiant M-Trends, CrowdStrike Global Threat Report,
  Verizon DBIR

### Output section in `[Subject]_Threat_Actor_Profiles.md`

```
## Targeting Summary
| Sector | Targeted? | Confidence | Source |
|--------|-----------|------------|--------|

## Named Victims (from public sources)
| Organization | Sector | Date | Actor | Source |
|--------------|--------|------|-------|--------|
```

---

## Phase 4: Exploitation Techniques & MITRE ATT&CK Mapping

**Goal:** Document how attackers are actually operating — from initial access through to impact —
mapped to the MITRE ATT&CK framework so defenders can correlate to their detections.

### MITRE ATT&CK sources

- **ATT&CK Navigator**: https://mitre-attack.github.io/attack-navigator/
- **ATT&CK Techniques**: https://attack.mitre.org/techniques/enterprise/
- **Software entries**: https://attack.mitre.org/software/ — for specific malware/tools
- **Campaigns**: https://attack.mitre.org/campaigns/ — named campaigns with full TTP mapping

### What to document for each technique cluster

Group techniques by ATT&CK tactic (Initial Access → Execution → Persistence → ... → Impact):

- **Technique ID + Name**: e.g., T1190 - Exploit Public-Facing Application
- **Sub-technique** if applicable: e.g., T1059.001 - PowerShell
- **How it's used in this context**: Concrete description, not just the ATT&CK abstract
- **Malware/tools observed**: Named tools (e.g., ZIPLINE, THINSPOOL, Cobalt Strike)
- **Actors using it**: Which groups from Phase 2 employ this technique
- **Detection opportunity**: Brief note on what defenders can look for (log sources, indicators)

### Common technique clusters to always check

- **Initial Access**: Phishing, exploitation of CVEs (from Phase 1), valid accounts, supply chain
- **Persistence**: Webshells, scheduled tasks, registry run keys, firmware implants (for OT)
- **Defense Evasion**: LOLBins, signed binary proxy execution, log clearing
- **Lateral Movement**: Pass-the-hash, RDP, SMB, WMI
- **Exfiltration**: Staging to cloud storage, encrypted channels, DNS tunneling
- **Impact**: Ransomware deployment, wiper malware, operational disruption

### Output: `[Subject]_TTP_Analysis.md`

```
# [Subject] — Exploitation Techniques & TTP Analysis

## ATT&CK Heatmap Summary
Tactics covered: [list]

## Technique Deep-Dives

### [Tactic]: [Technique Name] (TXXXX)
**How it's used:** [concrete description]
**Tools/malware:** [named tools]
**Actors:** [from Phase 2]
**Detection opportunity:** [brief note]

## Full TTP Table
| Tactic | Technique | ID | Actors | Tools | Detection |
|--------|-----------|-----|--------|-------|-----------|
```

---

## Phase 5: Trend Analysis & Watch List

**Goal:** Identify where the threat is heading — what's accelerating, what's new, and what
defenders should be watching in the next 30–90 days.

### Trend signals to look for

- **CVE surge**: Is vulnerability disclosure accelerating for this product/sector?
- **Actor pivots**: Are established APTs shifting targeting or techniques?
- **New initial access vectors**: Are attackers moving from phishing to exploitation or vice versa?
- **Ransomware ecosystem shifts**: New affiliates, new ransomware families, RaaS disruptions
- **Geopolitical triggers**: Escalating tensions that historically precede cyber campaigns
- **Law enforcement actions**: Takedowns that displace actors to new infrastructure/TTPs
- **Emerging tooling**: New malware families or offensive tools entering the wild
- **Zero-day trend**: Are zero-days being burned faster in this domain?

### Watch list format

```
## Recommended Watch List
| Item | Why it matters | Signal strength | Timeframe |
|------|---------------|-----------------|-----------|

## Trend Summary
[2–3 paragraphs synthesizing the forward-looking picture]
```

### Output: Appended to main brief as a final section

---

## Final Output: Master Briefing Document

Compile all phases into a single briefing document. Structure:

```
# [Subject] — Cyber Threat Intelligence Brief
**Prepared:** [date]
**Time window:** [e.g., Jan 2023 – present]
**Classification:** TLP:WHITE / TLP:CLEAR (adjust if needed)

## Executive Summary
[3–5 bullets: who's attacking, what's being exploited, who's targeted, key trend]

## 1. CVE & Advisory Landscape
[Phase 1 content]

## 2. Threat Actor Profiles
[Phase 2 content]

## 3. Targeted Organizations & Industries
[Phase 3 content]

## 4. Exploitation Techniques (ATT&CK Mapped)
[Phase 4 content]

## 5. Trend Analysis & Watch List
[Phase 5 content]

## Appendix: Sources
[All sources consulted, with links]
```

Produce both `.md` and `.docx` versions. Use the `docx` skill for the `.docx` — real tables,
proper headings, page numbers. The `.docx` is for sharing with leadership and IR teams.

---

## Key Sources Reference

### Always check (official agencies)
**US:**
- NVD: https://nvd.nist.gov
- NIST NCCoE: https://www.nccoe.nist.gov
- MITRE CVE: https://cve.mitre.org
- CISA KEV: https://www.cisa.gov/known-exploited-vulnerabilities-catalog
- CISA Advisories: https://www.cisa.gov/news-events/cybersecurity-advisories
- CISA ICS: https://www.cisa.gov/ics-advisories
- FBI Cyber: https://www.ic3.gov
- NSA Advisories: https://www.nsa.gov/Press-Room/Cybersecurity-Advisories-Guidance/
- MS-ISAC: https://www.cisecurity.org/ms-isac/advisories
- MITRE ATT&CK: https://attack.mitre.org

**EU & UK:**
- NCSC (UK): https://www.ncsc.gov.uk/section/keep-up-to-date/alerts-advisories
- ENISA Threat Landscape: https://www.enisa.europa.eu/publications/enisa-threat-landscape
- CERT-EU: https://cert.europa.eu/publications/security-advisories
- BSI (Germany): https://www.bsi.bund.de/EN
- ANSSI (France): https://www.cert.ssi.gouv.fr
- NCSC-NL (Netherlands): https://www.ncsc.nl/english
- CCN-CERT (Spain): https://www.ccn-cert.cni.es/en
- EU CyCLONe: https://www.enisa.europa.eu/topics/cyber-crisis-cooperation/cyclone

### Threat intelligence & research
- Mandiant/Google TAG: https://www.mandiant.com/resources/blog
- CrowdStrike: https://www.crowdstrike.com/adversaries/
- Microsoft MSTIC: https://www.microsoft.com/en-us/security/blog/topic/threat-intelligence/
- Recorded Future: https://www.recordedfuture.com/research
- SecurityWeek: https://www.securityweek.com
- The Hacker News: https://thehackernews.com
- Krebs on Security: https://krebsonsecurity.com

### Domain-specific sources (add automatically based on subject)
- **OT/ICS**: Dragos, Claroty, Nozomi Networks, ICS-CERT
- **Cloud**: Wiz Research, Orca Security, Google Project Zero
- **Ransomware**: ransomwatch, ID Ransomware, BleepingComputer
- **Nation-state (China)**: CISA China-specific advisories, Recorded Future Insikt Group
- **Nation-state (Russia)**: CISA Russia advisories, NCSC UK, EU CyCLONe
- **Nation-state (DPRK)**: US-CERT, Treasury OFAC notices, Chainalysis (for crypto theft)
- **Nation-state (Iran)**: CISA Iran advisories, ClearSky, Mandiant

Plus any URLs the user provided.

---

## Handoff & Continuity

At the end of every session, save a `BRIEF_HANDOFF.md` alongside the output files with:
- Research subject and time window
- Phases completed
- Sources consulted
- Key statistics (CVE counts, actors profiled, advisories reviewed)
- Files created
- Open threads / what to research next

This ensures a future analyst or Claude session can pick up exactly where things left off.
