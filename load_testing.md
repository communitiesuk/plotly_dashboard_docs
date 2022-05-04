# Assumptions

We have used a chrome plug-in, SimilarWeb to investigate other sites which could have a similar audience as our dashboards - LG Inform and LGC plus. These sites seem to average in the range of 50k users a month.

We will break our estimate down into 2 scenarios - General usage and Launch day (expecting a higher usage on launch)

## General usage estimate: 
500000 monthly users / 30 days average in a month = 1667 daily users <br>
Assuming users are active for 12 hours a day, taking into consideration lower usage during the evening and night: 1667 daily users / 12 = 139 users per active hour <br>
Breaking that down into a 20 minute window equates to around 46 users.

## Launch day estimate:
We estimate the number of users on launch day to be 10 times that of our general usage estimate, equating to 460 users.


The load test will initally focus on user routes through the dashboard, instead of simply requesting each page indidually then moving onto the next test. The benefit of this approach is we can mimic a user making choices in the available dropdowns on each of the pages.

The first iteration of load testing will not use a web framework such as Cypress or Selenium WebDriver to control changes to the dropdowns on each of the pages. Instead the locust tests will issue new page requests with query strings values matching the desired options in the dropdowns. This will slightly skew the results as instead of an asynchronous XHR request being executed within the page, we are requesting a new page load that will have a larger page size.

# Findings

