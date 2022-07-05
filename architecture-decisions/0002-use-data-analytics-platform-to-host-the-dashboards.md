# 2. Use Data Analytics Platform to host the Dashboards

Date: 2021-11-22

## Status

Accepted

## Context

- Data for Insights (Azure) is a strategic area to host dashboards
- Data for Insights does not have the capability to host Python based dashboard. Risual will be building this capability soon.
- A interim Data Analytics Platform (AWS) hosting solution for Python dashboards was recently developed to allow these dashboards to be hosted until Data for Insights has this capability. 
- The Data Analytics Platform hosting solution will be shut down once the Data for Insights platoform can host Python dashboards.
- Our dashboards will contain sensitive policy content that should remain internal to the department.
- The Data Analytics Platform will provide authentication against DLUHC Active Directory. 

## Decision

- We will deploy our dashboards to the Data Analytics Platform until the Data for Insights Platform is ready

## Consequences and mitigations

- There will be time added to the delivery for the transition to the Data Analytics Platform.
- The Data for Insights Platform may not meet our hosting needs for our dashboard.
- We will need to factor in time to our roadmap to move away from the Data Analytics Platform.
- We will work closely with Risual to ensure Data for Insights meets our dashboard hosting needs.
