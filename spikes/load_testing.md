# Assumptions

We have used a chrome plug-in, SimilarWeb to investigate other sites which could have a similar audience as our dashboards - LG Inform and LGC plus. These sites seem to average in the range of 50k users a month.

The load test will focus on the 'Compare local authorities' page, as this is the page of interest for launch. The load test will focus on user routes through the dashboard, the benefit of this approach is we can mimic a user making choices in the available dropdowns on the 'Compare local authorities' page.

The load test will simulate XHR/Fetch requests on page updates - the application updates part of a page without performing a full page refresh. So when an element on a page of the dashboard is updated, an XHR request is made to perform the update. 

Prometheus will be used to gather the CPU Usage and Memory utilisation; the Locust user interface will provide response time and failed user request metrics.

We will break our estimate down into 2 scenarios - General usage and Launch day (expecting a higher usage on launch) and run each scenario load test for 15 minutes.

## Viewing XHR requests on a webpage
XHR requests that a dashboard page performs when a filter is changed can be found by:
1. Loading the dashboard in chrome
2. Right clicking and selecting inspect and then selecting network
3. Making a change on the dashboard page in chrome
4. Selecting request payload

## General usage estimate: 
500000 monthly users / 30 days average in a month = 1667 daily users <br>
Assuming users are active for 12 hours a day, taking into consideration lower usage during the evening and night: 1667 daily users / 12 = 139 users per active hour <br>
Breaking that down into a 20-minute window equates to around 46 users.


## Launch day estimate:
We estimate the number of users on launch day to be 10 times that of our general usage estimate, equating to 460 users.


# Findings
## General usage
We ran a load test where we spawned 1 user per second up to a maximum of 46 users, we ran this test for 15 minutes.
We repeated this test twice and the results were always within the same bounds. 


![CPU_general](/images/load_test/xhr-general/cpu.PNG) <br>
*CPU usage peaked at just under 13% across the duration of the load test.

![Memory utilisation](/images/load_test/xhr-general/memory_utilisation.PNG) <br>
*Throughout the load test the memory utilisation was not affected. 

![response_times_general](/images/load_test/xhr-general/response_times.PNG) <br>
*The median response times stayed consistent at 30ms per request. The 95 percentile was initially high, however it quickly dropped to the range of 40ms - 60ms for the duration of the load test.

![total_requests_per_second_general](/images/load_test/xhr-general/total_requests.PNG) <br>
*There were roughly 28 requests per second, of which none failed.

## Launch day
We did not run a launch day load test ....

# Conclusion
Based on our estimated usage of the dashboard, we expect it to be able to handle loads of up to 460 users at one time. However, at higher usage we can expect poorer performance, though this should not be longer than 1 second, which we believe to be within a reasonable range. [See this article on response times](https://www.nngroup.com/articles/response-times-3-important-limits/).

Due to the extremely high CPU usage in the launch day scenario it is recommended that we implement auto-scaling within Gov PaaS for this application to prevent the possibility of the dashboard falling over.

# Further Research
Upon code review, we were alerted to improvements around how we better simulate usage around our load tests, this will be tackled with another user story.
