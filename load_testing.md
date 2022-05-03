Assumptions

We have used a chrome plug-in, SimilarWeb to investigate other sites which could have a similar audience as our dashboards. LG Inform and LGC plus. These sites seem to average in the range of 50k users a month.
Breaking that down into a 20 minute window equates to around 22 users.



The load test will initally focus on user routes through the dashboard, instead of simply requesting each page indidually then moving onto the next test. The benefit of this approach is we can mimic a user making choices in the available dropdowns on each of the pages.

The first iteration of load testing will not use a web framework such as Cypress or Selenium WebDriver to control changes to the dropdowns on each of the pages. Instead the locust tests will issue new page requests with query strings values matching the desired options in the dropdowns. This will slightly skew the results as instead of an asynchronous XHR request being executed within the page, we are requesting a new page load that will have a larger page size.

Findings

