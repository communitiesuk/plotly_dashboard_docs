# 

Date: 20-April-2022

## Status

Pending

## Context

We explored and evaluated three load testing tools - K6, JMeter and Locust.io.

|                                        | K6                                | JMeter                              | Locust.io |
|----------------------------------------|-----------------------------------|-------------------------------------|-----------|
| Open source?                           | Y                                 | Y                                   | Y         |
| Free to use?                           | Y                                 | Y                                   | Y         |
| Available inside DAP?                  | N - Node not installed on the DAP | N - JMeter not installed on the DAP | Y         |
| Runnable on local machines?            | Y                                 | Y                                   | Y         |
| Runnable in github pipeline?           | Y                                 | Y                                   | Y         |
| Runnable without user interface?       | Y                                 | Y                                   | Y         |
| Language experience inside DLUHC team? | N                                 | N/A - GUI                           | Y         |
| Language                               | node.js (javascript)              | Java                                | Python    |

### Additional Detail
DAP has no external Internet access to download files. JMeter can be pulled across into the DAP by first downloading it onto the local machine and moving it via OneDrive, however this process can be tedious and time-consuming.

JMeter uses a GUI to define the tests which are then exported out into a .jmx file. This can then be used to run the tests without the use of a user interface

### Additional Technologies
Blazemeter can be used as an online testing framework to run all the above options in the cloud at a much larger scale than running locally / as part of a pipeline.

## Decision

Based on the investigation above, we have decided that Locust.io would be the most appropriate technology to use when load testing our dashboards.
This technology is the only one of those investigated that meet all our criteria.

The technologies mentioned above are for the most part functionally similar as they are all capable of load testing. The key differences between them are the languages they use and how tests are created e.g. programmatically vs GUI.

## Consequences

- We will have a set of load tests that can be run against a dashboard to identify the risks of falling over when going live to the public.
