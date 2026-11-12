# SeismoWatch Kivu

**Earthquake Register, Alert Classification and Response Tracker for the Great Lakes Region**

| | |
|---|---|
| **Student** | CYUBAHIRO Israel |
| **Student ID** | 20251IMA127 |
| **Course** | INSY 8311, Database Development with PL/SQL |
| **Instructor** | Eric Maniraguha |
| **GitHub** | https://github.com/cyubahiroisrael0-beep/israel_20251IMA127_seismoWatch |

## Problem statement

The Kivu rift, in the western branch of the East African Rift, is one of the most seismically active parts of the rift system. On 3 February 2008 an Mw 5.9 earthquake near Bukavu and Cyangugu caused widespread damage in DR Congo and Rwanda, with 1,090 injured and nearly 5,000 buildings damaged according to OCHA figures reported by d'Oreye et al. (2011).

Earthquake information for the region reaches the public mainly through global catalogues such as the USGS. These catalogues describe events, but they are designed to inform rather than to record local case handling: which alert level an event received, who was assigned to respond, and when the case was closed. Without such a record, late or unanswered alerts are hard to see.

## Objectives

**Main objective:** build an Oracle PL/SQL database that registers earthquake events for the Kivu region, classifies each into an alert level using rules stored in the database, and tracks the response from open to closed.

**Specific objectives:**
1. Design a normalized (3NF) schema for events, zones, alert rules, alerts, responders, response actions and audit records.
2. Load real earthquake records from the USGS catalogue for a 300 km study area around Lake Kivu.
3. Classify each event automatically with a PL/SQL function and reject impossible readings with triggers.
4. Track every alert through OPEN, ACKNOWLEDGED, IN_PROGRESS and CLOSED, and escalate alerts that wait too long.
5. Audit every data change and automate ingestion and escalation with scheduled jobs.

## Data source

USGS Earthquake Catalog (ComCat), accessed through the FDSN Event Web Service in CSV format: https://earthquake.usgs.gov/fdsnws/event/1/

The query and download notes are in [`data/README.md`](data/README.md). USGS-authored data are in the U.S. public domain, and the catalogue holds no personal information.

## Innovation

**Option:** Smart automation.
**Tools:** Oracle `DBMS_SCHEDULER`, a PL/SQL ingestion package, and a small Python loader (python-oracledb) that fetches the USGS CSV into a staging table.

- An hourly job processes newly staged events: it validates them, registers them and raises alerts.
- A daily job escalates alerts that have had no response action for 24 hours.
- A replay procedure re-inserts historical events so the automation can be demonstrated live.

All business rules stay in PL/SQL. The loader only moves data into the staging table.

## Planned tables

`SEISMIC_EVENT`, `EVENT_STAGING`, `ZONE`, `MONITORED_PLACE`, `ALERT_LEVEL`, `ALERT_RULE`, `ALERT`, `RESPONDER`, `RESPONSE_ACTION`, `AUDIT_LOG`

## Planned PL/SQL features

| Feature | Where it is used |
|---|---|
| Procedure `register_event` | Validates a staged event and inserts it, rejecting duplicates |
| Function `classify_alert` | Returns Green, Yellow, Orange or Red from magnitude, depth and distance to a monitored place |
| Procedures `open_response`, `close_response` | Move an alert through its status steps |
| Procedure `simulate_event` | Replays a historical event for demonstration |
| Cursor | Loops over open alerts older than 24 hours so the daily job can escalate them |
| Trigger (validation) | Rejects magnitude, depth, latitude or longitude values that are impossible |
| Trigger (status guard) | Blocks closing an alert that has no response action |
| Trigger (audit) | Writes every insert, update and delete to `AUDIT_LOG` |
| Restriction rule | Allows changes to `ALERT_RULE` only on working days |
| Package `seismo_pkg` | Groups the procedures, function and custom exceptions |
| Exceptions | Custom errors for invalid magnitude, duplicate event and invalid status change |

The alert levels are project rules used to demonstrate database logic. They are not an official earthquake warning service.

## Repository structure

```
docs/research/research_log.md
docs/proposal/SeismoWatch_Kivu_Phase0_Proposal.docx
docs/presentation/SeismoWatch_Kivu_Phase0_Presentation.pptx
data/
```

## References

Delvaux, D., Mulumba, J.-L., Sebagenzi, M. N. S., Fiama Bondo, S., Kervyn, F., & Havenith, H.-B. (2017). Seismic hazard assessment of the Kivu rift segment based on a new seismotectonic zonation model (western branch, East African Rift system). *Journal of African Earth Sciences, 134*, 831–855. https://doi.org/10.1016/j.jafrearsci.2016.10.004

d'Oreye, N., González, P. J., Shuler, A., Oth, A., et al. (2011). Source parameters of the 2008 Bukavu-Cyangugu earthquake estimated from InSAR and teleseismic data. *Geophysical Journal International, 184*(2), 934–948. https://doi.org/10.1111/j.1365-246X.2010.04899.x

Oracle. (n.d.). *Using jobs* [Oracle Database Administrator's Guide, 11g Release 1]. https://docs.oracle.com/cd/B28359_01/server.111/b28310/scheduse002.htm

U.S. Geological Survey. (n.d.-a). *FDSN event web service documentation*. https://earthquake.usgs.gov/fdsnws/event/1/

U.S. Geological Survey. (n.d.-b). *M 5.9 - 20 km N of Cyangugu, Rwanda* [Event page]. https://earthquake.usgs.gov/earthquakes/eventpage/iscgem13213409

U.S. Geological Survey. (n.d.-c). *How should I cite a USGS website or publication?* https://www.usgs.gov/faqs/how-should-i-cite-a-usgs-website-or-publication

U.S. Geological Survey. (2008). *Magnitude 5.9 - Lac Kivu region, Dem. Rep. of the Congo* [Archived earthquake summary]. https://webarchive.library.unt.edu/eot2008/20090508025943/http://earthquake.usgs.gov/eqcenter/eqinthenews/2008/us2008mzam
