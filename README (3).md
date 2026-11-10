# Threat-Informed Defense (TID) System

*A threat-informed defense program built from zero in a Healthcare/MedTech organization, and run by a team of two.*

Shitesh Jatla · Threat Hunting & Forensics Lead · Nov 2026

> **How to read this**
> - **2 minutes:** Part 1. What it is, what it has delivered, and the system in one picture.
> - **10 minutes:** add Part 2. Why it looks the way it does, what's MITRE's and what's mine, and one technique traced end to end.
> - **Practitioners:** Part 3 covers every stage, gate and handoff. Part 4 shows what was borrowed and how claims are kept honest.
>
> **Status labels used throughout:** *In production* (in routine daily use) · *Built* (complete and used when needed, such as point-in-time assessments) · *Approved* (formally approved for the organization) · *Partial* (part running, part planned) · *Pilot* (running on a trial basis) · *In testing* (being tested before use) · *Planned* (not started).

---

## Part 1 · Overview

### In one minute

In October 2021 I joined a healthcare/MedTech organization as its first threat hunter. There was no hunter or responder yet, and the priority with a suspicious machine was getting it back in service quickly, so it was reimaged. In healthcare, that gap matters: the systems at risk support the people who make and deliver patient care.

Over five years I designed and built one connected program to close it, and moved it from reactive, to proactive hunting, to threat-informed defense aimed at the attackers most likely to target the organization. It is now moving toward predictive. Intelligence decides what to defend against. Hardening blocks what can be blocked, and detection covers the rest. Hunting and incident response catch what slips through, and validation tests whether the defenses actually work. I lead the program and work with one analyst, supported by AI workflows. By the program's own assessment it reached HMM Level 3 on David Bianco's Hunting Maturity Model, scored with a six-level checklist I built (Levels 0–5), with Level 4 in progress. Three Level 3 items stay open: two telemetry-coverage items that wait on budget decisions outside the program, and dwell-time measurement, which is in progress.

Along the way I authored a multi-year maturity roadmap reported to leadership, and three of the four operating standards behind the program's governance framework, which has been formally approved.

This repo documents the methodology, not the organization.

### What it has delivered

*System-level results. Incidents are described only in general terms, without dates, systems or other identifying detail.*

- **Stopped earlier in the attack chain.** In the intrusions handled since the loop has been running, the attacker was stopped before credential access in most cases and at credential access in the rest. No ransomware deployment, lateral movement or data theft was observed in any of them.
- **Saw an attack coming.** In one case, decoy activity, dark-web movement and commercial intelligence pointed to the same internet-facing entry point. We modeled it as the likely way in and warned the owning team. The attack later came through that entry point and was stopped at credential access.
- **Every recurring technique has a deployed rule.** All 65 techniques that recur across the priority actors have at least one deployed detection rule. At least 60% of them have a rule validated to V3, meaning it was tested in the lab with Atomic Red Team or Caldera and the expected detection fired.
- **Investigation instead of reformatting.** I lead incident response, a function that didn't exist before this program.
- **Claims checked before they become incidents.** External exposure claims, such as dark-web access-sale posts, are validated first. Fabricated ones are closed as disproven instead of being declared as incidents.
- **Adopted by another team.** The cyber-operations team uses the tradecraft cluster library (playbooks that group priority actors by the techniques they share) for live alert triage.
- **Run by two people.** 8 AI workflows with 20+ agents: 7 live, and the adversary emulation and purple teaming workflow in testing.
- **Awareness.** In phishing simulations shaped around priority actors, fewer targeted users fell for the second campaign than the first. The campaigns had different targets, so this is an observation, not a controlled result.

*Not measured yet:* mean time to detect (MTTD), and coverage across the full set of tracked techniques. The program's 31-metric dictionary separates what can be measured now from what isn't instrumented yet, and those numbers will be reported once they are measured.

### The system at a glance

![TID operating loop: intelligence feeds awareness, external exposure, hunting and a harden-or-detect decision; hardening breaks choke points; detection is validated with Atomic Red Team and Caldera, hands Anchor alerts to incident response and Support and Visibility breadcrumbs to hunting; hunting escalates to incident response and turns findings into detections; external exposure sends incident candidates to incident response; deception feeds hunting and sends early signals to intelligence; lessons feed back into intelligence, with AI workflows and governance underneath](docs/operating-loop.png)

Read the picture top to bottom:

1. **Intelligence** tracks 70–80 actors, profiles about 25 of them in depth, and follows about 150 of their techniques. It feeds awareness, external exposure checks, hunting, and the hardening and detection work.
2. **External exposure** checks outside claims, such as leaked credentials or access for sale. Only the ones that hold up go to incident response, as incident candidates.
3. **Harden or detect.** If a technique can be hardened without breaking operations, hardening breaks that choke point. If not, detection engineering builds a rule.
4. **Validation** tests rules with Atomic Red Team and Caldera, and the evidence goes back to detection.
5. **Detection hands off by confidence.** Anchor rules, the high-confidence ones, go to incident response. Support and Visibility rules, which are lower-confidence or add context, become breadcrumbs for hunters.
6. **Hunting** runs on intelligence and breadcrumbs. Findings either escalate to incident response or become new detections.
7. **Incident response** works in two directions: forward to stop the attacker's next move, backward to establish what already happened.
8. **Deception** works at both ends: decoys pick up early signals that feed intelligence, and they're used inside hunts to catch what gets past every control.
9. **Everything learned, including decoy signals, flows back into intelligence**, so the next round starts sharper. AI workflows and governance run underneath all of it.

---

## Part 2 · Design

### How it evolved, and how it predicts

| Stage | When | What it looked like | What moved it on |
| --- | --- | --- | --- |
| Reactive | Before late 2021 | Suspicious machines were reimaged to restore service quickly | Investigation wasn't yet anyone's role |
| Proactive | Years 1–3 | Hunting matured along David Bianco's Hunting Maturity Model, scored with a checklist I built | Hunt findings needed a clear path to the teams that could act on them |
| Threat-informed | Years 3–5 | Defense aligned to the attackers most likely to target the organization, instead of defending everything. Matured against MITRE's threat-informed defense model, with D3FEND brought in to build out defensive measures, the weakest dimension | Knowing who was coming raised the next question: can we see them before they arrive? |
| Toward predictive | Now | Converging signals and deep actor profiles point to who is coming, how, and through which entry point, before compromise | Predictions aren't measured yet, and internal intelligence is incomplete |

**Four intelligence sources**

| Source | What it shows | Status |
| --- | --- | --- |
| Decoys | Attackers touching decoys instead of real systems: early, low-noise signals of what is actually reaching the organization | In production |
| Commercial threat intelligence | Trends: what priority actors are doing and where they are heading | In production |
| Dark-web and cybercrime monitoring | Movement: what is being discussed, sold and planned | In production |
| Internal intelligence | What's inside the organization: assets, software and how they connect | Partial: being built out |

**Tracking and profiling.** Tracking covers 70–80 actors, watching each one's notable, high-impact moves. Profiling goes deep on about 25: every move, with two years of context covering where they come from, which technologies they target, their techniques, procedures and methods, and the campaigns they have run. The profiled actors' movements drive the daily, weekly and monthly hunts.

**How a prediction forms.** A single signal is noise. When signals from more than one source point the same way and match a profiled actor's known behavior, they become a prediction: who is likely to come, how, and through which entry point. The prediction becomes a threat model, then a warning with a recommended action, and high-priority warnings need a recorded acknowledgement. That is how the entry point in the case under *What it has delivered* was identified before the attack arrived.

**Limits.** The program is predictive in practice, not yet in measurement. Predictions aren't logged and scored yet, so there is no hit rate to report, and a warnings log is the next step. Internal intelligence would sharpen predictions from *which actors and techniques* to *which of the organization's own systems*.

### What I set out to do

The goal was to build defenses that protect patients, colleagues and the organization, focused on the attackers who actually target healthcare.

1. **Investigate instead of reformat.** Find out what happened on a suspicious machine, and catch attackers before they reach their objective.
2. **Aim defense at real adversaries.** Focus on the actors who target healthcare and MedTech, not the whole ATT&CK matrix.
3. **Make it last.** Run the program on written standards, so it doesn't depend on the people running it today.

**Who's who.** I lead the program: I run hunts, lead incident response and act as senior reviewer. One analyst works with me on hunts and investigations. The cyber-operations team handles operations and first-line alert triage. In this README, "incident response" means the lead and the analyst working an investigation, and only that role declares an incident.

### The constraints, and the decisions they forced

Most design choices in this repo exist because of a constraint or a lesson. This table is the shortest way to understand why the system looks the way it does.

| Constraint or lesson | Decision it forced |
| --- | --- |
| Two people, with no separate SOC or incident response team | Track 70–80 actors broadly and profile about 25 in depth. Fold incident response into the hunter role. Use AI workflows to draft, with people deciding. |
| Tracking actors by hand exceeded working memory | A weekly intelligence baseline: actor profiles, change tracking and hunt priorities produced on a fixed cycle. |
| A weekly cycle can leave a new zero-day unseen for days | A daily intelligence workflow that triages new threats and turns the urgent ones into same-day hunts. |
| Picking rules from large libraries by overlap wasn't analytically strong enough | Model the behaviors that stay stable across actors' attack chains, and cluster the profiled actors into tradecraft playbooks. |
| Operations depend on systems that can't simply be locked down | Harden where operations allow. Anything that can't be hardened goes to detection first. |
| Response and hunting alone don't always stop the attacker's next move | Map choke points and deployable controls, so the teams that own them can break an attack chain early. |
| Two people can't chase every alert | Tier rules by confidence. Only Anchor rules interrupt incident response; the rest become breadcrumbs for hunters. |
| No git-based CI/CD for detection content | A spreadsheet lifecycle register that applies Detection-as-Code's review, approval and test-evidence controls. |
| A team of two can't review its own rules independently | A documented compensating review before a rule is promoted. |
| Hunting and responding at the same time, with tracing backward by hand being slow | Split each investigation in two: the lead works forward, the analyst works backward, both guided by the threat-informed IR workflow. |
| An endpoint tool closing an alert doesn't mean the incident is over | Investigations guided by tradecraft playbooks, so they check for the attacker's next stage, such as hidden persistence and command-and-control. |
| Detections were asserted, not measured | A validation pipeline that tests rules and reports each one at the level it has evidence for. |
| Intelligence that is sent is not the same as intelligence that is acted on | High-priority intelligence items need a recorded acknowledgement, not just a send, and every handoff gets a recorded disposition. |
| External claims arrive under pressure and with little evidence | A state model where a signal is never an incident on its own, with urgency, confidence and severity scored separately. |
| Maturity is self-assessed, with no external certification | Keep achieved, planned and validated work apart in every report. |

### From MITRE's principle to a running program

MITRE's Center for Threat-Informed Defense (CTID) defines threat-informed defense across three dimensions: cyber threat intelligence, defensive measures (prevention, detection and mitigation tailored to known threats), and test and evaluation. Its INFORM model (v2.0) breaks those dimensions into 22 components: 7 for intelligence, 10 for defensive measures, and 5 for test and evaluation. This program is built on that model.

INFORM describes what a mature program has. It doesn't say how a team of two builds it: what to act on first, what to block and what to watch, who acts on which signal, and how to know any of it works. Those decisions are the program. The table maps each one to the INFORM component it serves. It's a map, not a score: no formal INFORM assessment has been done.

| Dimension | INFORM v2.0 component | What this program decided |
| --- | --- | --- |
| Intelligence | Relevance of threat intelligence | **A funnel two people can act on:** 70–80 actors tracked, about 25 profiled, about 150 techniques followed, grouped into 4 tradecraft clusters plus a fifth for emerging tradecraft. Four sources feed it: decoys, commercial intelligence, dark-web monitoring and internal intelligence. |
| Intelligence | Recency; speed of dissemination | **Two cadences:** a daily workflow for new threats and a weekly actor baseline, plus a monthly hunt. |
| Intelligence | Incorporation; operational integration; CTI-driven decision making | **Every item gets a recorded disposition** that routes it to hunting, hardening, detection, validation, awareness or incident response. High-priority items need an acknowledgement back. |
| Defensive measures | Prevention and mitigation (the dimension's scope) | **Harden first, detect the rest.** Awareness, shaped around priority actors, covers attacks that start with a person. |
| Defensive measures | Data collection | **Telemetry readiness is checked before every hunt and rule.** Every technique gets a detection decision, including a documented visibility gap where it can't be seen yet. |
| Defensive measures | Detection rules; propagation between CTI and detections | **Two lenses pick what gets a rule** (the 65 recurring techniques and CTID's top 10), and **tiers decide who acts:** Anchor to incident response, Support and Visibility to hunters. |
| Defensive measures | Threat hunting | **Hypothesis-led hunts** fed by intelligence and detection breadcrumbs. Findings escalate to incident response or become new detections. |
| Defensive measures | Incident response | **A two-direction investigation:** the lead works forward to stop the attacker's next move, the analyst works backward through the evidence. Tradecraft playbooks guide both, and attribution always carries a confidence level. |
| Defensive measures | Incident recovery and forensics | **Backward reconstruction from evidence**, keeping observed and inferred apart. Lessons feed detection, hardening and intelligence. |
| Defensive measures | Deception | **Decoys as an early-warning sensor** that feeds intelligence, and as traps inside hunts. Intelligence-driven design with MITRE Engage is next. |
| Defensive measures | No single component | **A dark-web claim is never an incident on its own.** External exposure has its own state model, and only incident response declares an incident. |
| Test and evaluation | Test focus; test relevance | **Test objectives come from the tradecraft clusters** and the recurring techniques. |
| Test and evaluation | Test planning; test triggers | **A governed plan with scope and a Ready / Not-ready gate**, triggered by new or changed rules, intelligence objectives, or recurring techniques without evidence. |
| Test and evaluation | Test results | **Each rule is reported at the highest validation level it has evidence for**, from V0 to V5, and the evidence goes back to detection. A clean pilot is not evidence of recall. |
| Running it | — | **AI drafts, people decide.** A named person approves every production rule, attribution and risk acceptance. |

### One technique, traced through the system

*Synthetic and illustrative. It shows how the decisions connect, not a real case.*

**Technique:** PowerShell execution (T1059.001).

1. **Intelligence.** The technique recurs across several of the profiled actors and appears in more than one tradecraft cluster, so it lands in the recurring set. Disposition: **Hardening**.
2. **Harden or detect.** Blocking PowerShell outright would break endpoint management, which depends on scripted execution. The hardening that doesn't break operations is routed to the control owner and recorded on the D3FEND coverage map under Harden. The rest goes to detection engineering. **Decision: partial harden, then detect.**
3. **Detection engineering.** A GOST hypothesis (the attacker's Goal, Objective, Strategy and Tactics) describes what the attacker is trying to achieve with the script. Telemetry readiness is confirmed. The rule is split by confidence:
   - Encoded download-and-execute from an unexpected parent process → **Anchor**
   - Unusual script execution that overlaps with management tooling → **Support**, backing the Anchor and catching the variants it can't safely alert on

   The pilot ends in **Promote**. On the D3FEND coverage map, the rule is recorded under Detect as Script Execution Analysis (D3-SEA) and Process Lineage Analysis (D3-PLA).
4. **Validation.** An Atomic Red Team test for T1059.001 runs in the lab, the expected detection fires, and the rule is reported at **V3**.
5. **Incident response.** When the Anchor rule fires, incident response takes over without waiting for certainty.
6. **Hunting.** Support hits become breadcrumbs for the weekly hunt. The hypothesis states up front what evidence would support it and what would refute it. One possible finding: a new parent-process pattern that legitimate tooling never produces.
7. **Back into the loop.** That finding returns to detection as a **detection opportunity**, and to intelligence as an **intelligence finding** that updates the actor and technique records. The next round starts sharper.

Each handoff ends in a named outcome, which is what makes this a connected system and not a set of separate tools.

### Defenses tracked in D3FEND

ATT&CK describes what attackers do. MITRE D3FEND describes the defensive techniques that counter them. D3FEND is this program's defensive vocabulary, and controls are tracked against it.

The D3FEND coverage map records, for each prioritized ATT&CK technique, the D3FEND countermeasures mapped to it and which of them are in place. That shows coverage as defenses, not only as detections. The ATT&CK→D3FEND mapper feeds the same map when it builds the hardening backlog.

The map was built on D3FEND's five original tactics, and all five are in use:

| D3FEND tactic | Where it lives in this program |
| --- | --- |
| Harden | Hardening |
| Detect | Detection engineering, with hunting following the Support and Visibility breadcrumbs |
| Isolate | Incident response containment |
| Deceive | Decoys used inside hunts today; intelligence-driven design with MITRE Engage next |
| Evict | Incident response eradication, such as credential resets and removing persistence |

D3FEND has since added Model and Restore. They aren't on the map yet.

---

## Part 3 · Reference: the loop, stage by stage

Every stage below has a trigger, a method, a gate and an output; deception is the one exception, noted in its section. Two design rules run through all of them:

- **Urgency, confidence and severity are scored separately.** How fast to look, how sure we are, and how bad it is are three different questions, and collapsing them is how unverified claims become incidents.
- **Every handoff gets a recorded disposition.** Publishing intelligence, notifying a team or enabling a rule is not the same as acting on it.

The tradecraft clusters are the backbone: the same playbooks feed hunting, incident response, detection engineering, hardening and validation, so every stage works from one shared picture of the adversary.

### 1. Intelligence (CTI)

- **Scope:** 70–80 actors tracked for notable moves; about 25 profiled in depth with two years of context; about 150 of their techniques followed, with daily, weekly and monthly hunts driven by the profiled actors' movements
- **Sources:** decoys, commercial threat intelligence, dark-web and cybercrime monitoring, and internal intelligence (partial). A prediction needs signals from more than one source that match a profiled actor's behavior
- **Two cadences:** a weekly baseline (actor profiles, change tracking, hunt priorities and detection guidance) and a daily workflow that triages new threats and vulnerabilities, so urgent ones don't wait for the weekly cycle
- **Actor governance:** each actor is enriched, graded for source reliability and analytic confidence, scored for relevance to the organization, peer-reviewed, and approved by the program lead before it enters the priority list
- **Trigger:** a priority intelligence requirement with a named consumer and a decision it supports; the daily and weekly actor changes; credible exploitation of an applicable vulnerability
- **Method:** source reliability and information credibility graded separately; analytic confidence (High, Moderate, Low, Unknown) kept apart from severity; actors tiered on intent, capability, opportunity and sector and technology overlap; techniques prioritized by cross-actor recurrence and attack-chain convergence, which produces the tradecraft clusters (4 clusters plus a fifth for unclassified or emerging tradecraft); vulnerability intelligence in two lanes, with CTI supplying exploitation context and vulnerability management owning remediation
- **Gate:** every actionable item gets an intelligence-to-action disposition: Not relevant, Monitor, Hunt, Detection, Vulnerability action, Hardening, Validation or Incident support. High-priority items need an acknowledgement, not just a send. In the daily workflow, each new item gets an inform-or-hunt decision: share it for awareness, or open a hunt
- **Output:** warnings with a recommended action when converging signals point to a likely entry point; hunt briefs to hunting; actionable items to hardening and detection engineering; detection objectives to validation; incident support to incident response; awareness inputs; external signals to external exposure. Handoffs carry confidence, time sensitivity, required telemetry and limitations, and operational feedback returns to update actor, technique and confidence records
- **Count integrity:** prioritized, hunted, detected, partially covered and validated techniques are counted separately and never used interchangeably. A technique counts as validated when at least one of its rules has reached V3

### 2. External exposure (runs beside intelligence)

- **Trigger:** an external signal such as an underground mention, an access-sale advertisement, a leaked credential, a leak-site listing, a lookalike domain or a supplier breach, whether surfaced by intelligence monitoring or reported directly
- **Method:** a state model of Signal → Finding → Exposure → Incident candidate → Incident, where most signals stop early. Validation Priority (Urgent, Elevated, Routine) sets how fast to look; Confidence (Unverified to Confirmed) records how sure we are; Severity is organizational impact × exploitability. Exposure Locus records where it came from: Organizational, Third Party, External/Unmanaged, Mixed or Unknown
- **Gate:** a signal is never an incident on its own, and only incident response declares one. Closure needs a final disposition, including *Disproven / Fabricated Claim*; notifying a supplier or another team is not closure
- **Output:** incident candidates to incident response; telemetry checks to hunting; referrals to identity, vendor owners, or privacy and legal; outcomes, including disproven claims, back to intelligence. Initial, peak and closing severity are all kept

### 3. Awareness

- **Trigger:** approved actor profiles and current changes in how priority actors target people
- **Method:** human-targeting trends are assessed for relevance and urgency to the organization, then turned into awareness recommendations and phishing simulations shaped around what priority actors actually do
- **Gate:** an intelligence review for accuracy, then approval by the awareness owner before anything is published
- **Output:** approved awareness actions and simulation themes; results feed back to intelligence

### 4. Hardening

- **Trigger:** actionable items from intelligence, control weaknesses from hunts, *Harden* dispositions from detection pilots, and hardening changes from incident response
- **Method:** maps the choke points in priority actors' attack chains and the controls that could break them; the ATT&CK→D3FEND mapper turns them into a backlog. Where a technique can be hardened without breaking operations, it is made difficult or ended earlier
- **Gate:** a control-deployability decision, then acceptance by the team that owns the control
- **Output:** a hardening backlog routed to owning teams and recorded on the D3FEND coverage map; techniques that can't be hardened go to detection engineering
- **Status:** hardening runs as its own workstream; the AI-assisted choke-point tracker and D3FEND mapper are in pilot

### 5. Detection engineering

- **Trigger:** a technique that can't be hardened without breaking operations, a hunt finding, an intelligence handoff, an incident lesson, a validation gap, a coverage or visibility assessment, or third-party content
- **Priority:** two lenses: the 65 techniques that recur across priority actors, and the top 10 from CTID's Top ATT&CK Techniques
- **Method:** a build, source, enhance or defer decision for each requirement, with outside content treated as untrusted until evaluated locally; a GOST threat model and hypothesis (Binary Defense TIDE); each rule tiered Anchor (highest signal, fewest false alarms), Support (backs an Anchor, or stands in where no Anchor is possible) or Visibility (context that improves investigation); telemetry readiness checked; peer review, with a documented compensating review where a team of two can't be fully independent; then a pilot
- **Gate:** every pilot ends in a disposition: Promote, Tune and retest, Escalate (raise for a senior decision), Harden, Remediate telemetry, Defer, Reject or Risk accept. A clean pilot is not evidence of recall
- **Output:** production rules with a specification, investigation guidance and health monitoring, tracked in the detection lifecycle register; new and changed rules to validation; Anchor alerts to incident response; Support and Visibility hits to hunters as breadcrumbs

### 6. Validation

- **Trigger:** a new or changed rule from detection engineering, a detection objective from intelligence, or a recurring technique without evidence
- **Method:** test objectives grounded in the tradecraft clusters; a governed plan with scope and rules of engagement; safe, lab-only tests with Atomic Red Team and Caldera; results checked against the detections that were expected to fire
- **Gate:** a Ready / Not-ready decision and scope approval before any test runs. Uncertain gaps go to the senior reviewer instead of being accepted automatically
- **Validation levels:** V0 not validated, V1 peer reviewed, V2 evaluated on our telemetry, V3 technique-tested with Atomic Red Team or Caldera, V4 adversary-emulation tested, V5 purple-team validated. Levels build on each other, and each rule is reported at the highest level it has evidence for
- **Output:** evidence and confirmed gaps back to detection engineering; gaps that need a look in live telemetry become validation-driven hunts
- **Status:** rules reach V3 today. The workflow for full adversary emulation (V4) and purple teaming (V5) is in testing, and making the evidence loop back to detection formal and persistent is in progress

### 7. Hunting

- **Trigger:** hunt briefs from intelligence (daily, weekly and monthly), Support and Visibility breadcrumbs from detection, telemetry checks from external exposure, validation gaps, decoy activity, or one of eleven hunt types, including intelligence-led, incident-derived, detection-gap, visibility-assessment, validation-driven, baseline and analyst-generated
- **Method:** a testable hypothesis that states up front what evidence would support it and what would refute it; ATT&CK mapped to the behavior actually hunted, never to inflate coverage; benign explanations tested deliberately; causal-chain pivots when something looks wrong; decoys used inside hunts
- **Gate:** starting needs an approved hunt brief and a telemetry-readiness call (Ready, Ready with limitations, Partially ready, Not ready, Unknown), and a hunt goes ahead with limitations only when they are documented. Every query is reviewed before it runs in production. Closing needs a hypothesis disposition (Supported, Partially supported, Not supported, Inconclusive, Not testable, Superseded) and a documented closure review
- **Output:** every finding is typed, and each type has a primary action. Confirmed malicious goes to incident response, a detection opportunity to detection engineering, a telemetry gap to the visibility gap register, a control weakness to hardening, an intelligence finding back to intelligence. *No material finding* is reported as a scoped result, never as proof of absence

### 8. Incident response

- **Trigger:** an Anchor alert, a hunt finding past escalation criteria, an exposure that becomes an incident candidate, or an intelligence item marked *Incident support*
- **Method:** every investigation runs in two directions at once. The lead hunter works **forward**: where the attacker is now, and what their likely next move is according to the tradecraft playbooks, so it can be stopped. The analyst works **backward**: what the attacker has already done, established from the evidence. The threat-informed IR workflow guides both directions, so neither stops when one tool closes an alert. Escalate without waiting for certainty; scope with telemetry and topology; isolate, contain and evict; keep observed and inferred apart. Attribution, inside the same workflow, always carries a confidence level. Response decisions on a credible compromise are escalated through the leadership path with the evidence on record. 
- **Gate:** only incident response declares an incident; attribution goes through a confidence review; hunt and incident records are linked both ways
- **Output:** lessons back to intelligence, including actor reassessment when attribution warrants it; new detections to detection engineering; hardening changes to hardening; governance gaps

### 9. Deception

- **Two roles:** a sensor at the start of the loop, and a trap at the end of it
- **Trigger:** activity on a decoy, such as scanning, connection attempts or use of a planted artifact
- **Method:** as a sensor, decoy activity is treated as an intelligence signal and cross-checked against commercial intelligence, dark-web monitoring and actor profiles, in line with MITRE Engage's Expose and Elicit goals. As a trap, decoys are used inside hunts, where any interaction is a strong lead, because nothing legitimate should touch them
- **Gate:** decoy signals enter intelligence and hunting and follow their gates. Deception doesn't have its own formal gate yet; that comes with the MITRE Engage design
- **Output:** early signals to intelligence; leads to hunting
- **Next:** decoy design driven by intelligence on the actors who target the organization, following MITRE Engage

### Underneath: AI and governance

- **AI workflows:** 8 workflows with 20+ agents, covering intelligence, tradecraft playbooks, hunting, query building, incident response, detection engineering, hardening, validation and governance. 7 are live; the adversary emulation and purple teaming workflow is in testing. AI may draft, extract, map and translate, but it never approves a production rule, assigns attribution, accepts risk or closes an intelligence requirement; a named person does
- **Governance:** a framework and operating standards per stage, a conformance model (Conformant, Partial, Planned, Exception) across about 150 requirements, time-bound exceptions, and metrics that keep requirement, production, validated, degraded and retired states apart

### Where humans decide

AI and automation scale the work; these decisions stay with people.

| Decision point | What is decided |
| --- | --- |
| Actor approval | Whether an actor enters the priority list, after peer review and lead approval |
| Inform or hunt | Whether a new item is shared for awareness or becomes a hunt |
| Query execution | Whether a query may run in production |
| Hunt evidence | Whether the evidence supports the hunt record |
| Detection deployment | Whether a rule goes to production |
| Control deployability | Whether a hardening control is feasible before it reaches the owning team |
| Validation readiness and scope | Whether a test may run, and within what scope |
| Uncertain validation gaps | How an unclear result is treated, decided by the senior reviewer |
| Attribution confidence | How confident an attribution is; it is never stated without a confidence level |
| Incident declaration | Whether it's an incident; only incident response declares one |
| Risk acceptance | Whether a residual risk is accepted, by a named person |
| Closing an intelligence requirement | Whether a requirement has been answered |

### What's next

- A warnings log: every prediction recorded with its date, what was predicted, who was told, what they did and what happened, so the program can measure how often its predictions are right
- Deeper internal intelligence, so predictions can name the organization's own systems
- Make the validation-to-detection feedback loop formal and persistent
- Full adversary emulation (V4) and purple-team exercises (V5)
- Intelligence-driven deception design with MITRE Engage
- Add D3FEND's Model and Restore tactics to the coverage map
- A shared knowledge layer that links actors, techniques, hunts, detections, controls and validation results, so the stages stop depending on manual handoffs

---

## Part 4 · Provenance and honesty

### What this borrows and what it adds

The connected system is the contribution. Some methods are built directly on published work, marked *Built on* or *Adapted from*. Others were built for this program independently and later found to match published work, marked *Aligned with* or *Related*.

**Built on, used deliberately:** MITRE CTID threat-informed defense (three dimensions, INFORM v2.0) · MITRE ATT&CK · MITRE D3FEND · MITRE Engage · Hunting Maturity Model by David Bianco (Levels 0–4; I added Level 5 and wrote the 91-item checklist) · Atomic Red Team · Caldera · SOC Prime (rule sourcing and the AI benchmark) · Binary Defense TIDE field guide and datasheet (2025): GOST threat modeling, hypothesis format, Detection-as-Code workflow, and fidelity, coverage, MTTD and drift metrics

#### Intelligence

| Method | What it does | Status | Relation to published work |
| --- | --- | --- | --- |
| Tradecraft cluster library | Profiled actors grouped into 4 tradecraft clusters plus a fifth (TC-5) for unclassified or emerging tradecraft. Each carries phase signposts, hunt queries, triage steps, telemetry requirements and containment guidance. Sibling clusters share mid-chain hunts. Reviewed every 90 days. | In production | Aligned with: Sophos X-Ops threat activity clusters (2023); academic clustering of ATT&CK technique co-occurrence |
| Tradecraft prompt chain | Produces a sector-specific cluster framework from any actor dossier | Built | — |
| Actor governance and prioritization | 70–80 actors tracked and about 25 profiled with two years of context. Enrichment, source and confidence grading, weighted relevance scoring, peer review and lead approval; tiered defend-lists with a next action and a review clock per actor | In production | — |
| Change tracking (daily and weekly) | Turns new actor techniques, tooling, infrastructure and exploitation into behaviors to hunt, routed to control owners | In production | — |
| Two-lane vulnerability intelligence | Actor-driven lane (initial-access CVEs that priority actors use) plus exploited-edge lane (actively exploited edge and access-layer CVEs) | In production | Related: CTID Prioritize Known Exploited Vulnerabilities |
| Exposure claim validation | Validates dark-web access-sale claims and separates supplier exposure from internal compromise, bounded by telemetry and topology | In production | — |
| Deception as an intelligence sensor | Decoy activity treated as an early-warning signal and cross-checked with commercial intelligence, dark-web monitoring and actor profiles | In production | Built on: MITRE Engage (Expose and Elicit goals) |
| Prediction from converging signals | Signals from more than one source, matched to a profiled actor, become a threat model and a warning with a recommended action | Partial: predictions aren't logged or scored yet | — |
| Threat-informed awareness | Turns priority actors' human-targeting trends into approved awareness actions and phishing simulations | In production | — |

#### Hunting

| Method | What it does | Status | Relation to published work |
| --- | --- | --- | --- |
| Maturity assessment and roadmap | 91-item checklist across six levels (0–5); a level counts as met at 75% of its items, with its defining items met; multi-year roadmap reported to leadership | In production | Built on: Hunting Maturity Model (Bianco) |
| Intelligence-led hypotheses | Hypotheses generated from intelligence, mapped to ATT&CK, refined through feedback | In production | Aligned with: PEAK and TaHiTI (intel-driven hunting); CTID INFORM v2.0 Threat Hunting component |
| Deception-assisted hunting | Deployed decoys used inside hunts | In production | — |
| Intelligence-driven deception design | Decoy design and engagement driven by intelligence on targeted actors | Planned | Built on: MITRE Engage |
| Hunt-to-detection workflow | Hunt findings become custom detection logic, refined continuously | In production | Aligned with: PEAK and similar frameworks |

#### Detection engineering, validation and hardening

| Method | What it does | Status | Relation to published work |
| --- | --- | --- | --- |
| Anchor / Support / Visibility tiers | Anchor = highest-signal incident trigger; Support = backs an Anchor or stands in where none is possible; Visibility = investigation context and hunting signal | In production | Not in Binary Defense TIDE material (checked); related: risk-based alerting |
| GOST behavioral hypotheses | Each detection starts from the attacker's Goal, Objective, Strategy and Tactics, mapped to observable behavior and telemetry | In production | Built on: Binary Defense TIDE (GOST model) |
| Recurrence-based technique priority | The 65 techniques that recur across priority actors drive the rule backlog, alongside the top 10 from CTID's Top ATT&CK Techniques | In production | Aligned with: CTID Top ATT&CK Techniques; Binary Defense TIDE step 1 (prioritize by likelihood and impact, not the whole matrix) |
| Detection quality controls | Telemetry requirements, noise and suppression decisions, backtesting, live-trigger review | In production | Aligned with: Binary Defense TIDE telemetry requirements, fidelity measurement, false-positive tuning |
| Validation pipeline | Intelligence-grounded test objectives, governed plans, lab-only Atomic Red Team and Caldera tests, and checks against expected detections; full adversary emulation is in testing | Partial | Built on: Atomic Red Team and Caldera, as Binary Defense TIDE prescribes |
| Detection lifecycle register | Detection-as-Code lifecycle run as a spreadsheet register, operable by a two-person team without git-based CI/CD | In production | Adapted from: Binary Defense TIDE Detection-as-Code (git CI/CD) |
| ATT&CK visibility assessment | Full, partial or no visibility per prioritized technique, producing a prioritized detection backlog | Built | Related: DeTT&CT |
| Pentest-informed assessment | Correlates pentest activity with defensive evidence by log source to expose telemetry gaps | Built | — |
| Choke-point tracker and D3FEND mapper | AI-assisted mapping of choke points to D3FEND controls, producing a hardening backlog routed to owning teams | Pilot | Built on: D3FEND's ATT&CK mappings |
| D3FEND coverage map | Records, for each prioritized ATT&CK technique, the D3FEND countermeasures mapped and in place across the five original tactics | In production | Built on: MITRE D3FEND |

#### AI-assisted workflows

| Method | What it does | Status | Relation to published work |
| --- | --- | --- | --- |
| Functional workflows | 8 workflows with 20+ agents; people approve every deployment | 7 in production, 1 in testing | — |
| Query Builder | Generates query-language-specific detections using the environment's field names | In production | — |
| Benchmark evaluation | Prompts iterated against ten SOC Prime-covered behaviors before use | Built | — |

#### Governance

| Method | What it does | Status | Relation to published work |
| --- | --- | --- | --- |
| Framework and operating standards | Closed-loop lifecycle framework; I authored three of the four operating standards (hunting, detection engineering and validation, intelligence integration) | Approved | — |
| Conformance model | Conformant, Partial, Planned or Exception, across about 150 requirements | Built | — |
| Metrics dictionary | 31 metrics, each with formula, source, owner, cadence and caveat; measurable-now separated from not-yet-instrumented | Built | Adopted from Binary Defense TIDE: fidelity, coverage, MTTD and drift metrics |

#### Investigation and response

| Method | What it does | Status | Relation to published work |
| --- | --- | --- | --- |
| Threat-informed incident response | Two-direction investigation: the lead works forward to stop the attacker's next move, the analyst works backward to scope what already happened, both guided by the tradecraft playbooks; attribution is confidence-rated and can trigger actor reassessment | In production | Aligned with: CTID INFORM v2.0 Incident Response and Incident Recovery and Forensics components; the two-direction method is this program's |
| Incident-to-defense feedback | Case findings converted into detections, hardening changes and governance gaps | In production | Standard lessons-learned practice; the routing is the addition |

### Evidence discipline

Every claim in this program is bounded by five distinctions, and the repo keeps them.

| Distinction | How it shows up here |
| --- | --- |
| Observed vs inferred | A probable entry point stays probable, and outcomes say what was observed, not what was prevented |
| Deployed vs validated | Every recurring technique has a deployed rule; at least 60% have a rule validated to V3 |
| Achieved vs planned | Predictive in practice, not yet measured; V4 emulation and V5 purple-team validation are planned or in testing, not achieved; MTTD isn't measured yet |
| Assessed vs certified | HMM Level 3 is the program's own assessment, scored with my checklist: a level counts as met at 75% of its items, with all of its defining items met and every open item named. No external body certifies it |
| Correlated vs causal | Awareness results across two campaigns are observational, with different targets and denominators |

---

## License

© 2026 Shitesh Jatla. All rights reserved. Shared for reading; please ask before reusing.
