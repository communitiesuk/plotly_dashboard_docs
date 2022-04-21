# 

Date: 20-April-2022

## Status

Pending

## Context

We explored and evaluated three open source load testing tools - K6, JMeter and Locust.io.

Blazemeter can be used as an online testing framework to run all the below options in the cloud at a much larger scale than running locally / as part of a pipeline

|                                        | K6   | JMeter    | Locust.io |
|----------------------------------------|------|-----------|-----------|
| Open source?                           | Y    | Y         | Y         |
| Free to use?                           | Y    | Y         | Y         |
| Available inside DAP?                  | N - Node not installed on the DAP   | N - JMeter not installed on the DAP       | Y         |
| Runnable on local machines?            | Y    | Y         | Y         |
| Runnable in cloud?                     | Y    | Y         | Y         |
| Runnable in github pipeline?           | Y    | Y         | Y         |
| Runnable without user interface?       | Y    | Y         | Y         |
| Language experience inside DLUHC team? | N - Unable to use node.js | N/A - GUI | Y         |

### Additional Detail
DAP has no external Internet access to download files. Files need to be installed in DAP through DAP support, which has a lead time associated.


## Decision

Based on the investigation above, we have decided that Locust.io would be the most appropriate technology to use when load testing our dashboards.
This technology is the only one of those investigated that meet all our criteria.


## Consequences

- We will have a set of load tests that can be run against a dashboard to identify the risks of falling over when going live to the public.
