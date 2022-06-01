# Assumptions
This is the second load test to be performed on the Local Government dashboard. Please see [here](/spikes/1-basic_load_test) for previous work. 

This load test will focus on the 'Compare local authorities' page, as this is the page of interest for launch. We will focus on user routes through the dashboard, the benefit of this approach is we can mimic a user making choices in the available dropdowns on the 'Compare local authorities' page.

The load test will simulate XHR/Fetch requests on page updates - the application updates part of a page without performing a full page refresh. So when an element on a page is updated, an XHR/Fetch request is made to perform the update. 

![XHR_requests_example](/images/load_test/xhr-general/xhr_requests.png) <br>

As the above image shows, upon selecting a filter multiple XHR requests are made to populate the page. These XHR requests can be upwards on 1mb in size and take longer than 1 second to complete. 

The load test will use the same estimates as the previous load test.

Note: The dashboard has autoscaling configured, with a maximum of 2 instances.

## Testing method
These tests were modelled on the requests shown within the chrome developer tools as we ran through a user journey on the 'Compare local authorities' page.

XHR/Fetch requests can be found by:
1. Loading the dashboard in chrome
2. Pressing F12 
3. Making a change on the dashboard page in chrome
4. Selecting an updated component 
5. Selecting request payload

# Findings
## General usage
We ran a load test where we spawned 1 user per second up to a maximum of 46 users, we ran this test for 15 minutes.
We repeated this test twice and the results were always within the same bounds. 

![CPU_general](/images/load_test/xhr-general/cpu.PNG) <br>
CPU usage on both instances was roughly 100% across the duration of the load test.

![Memory utilisation](/images/load_test/xhr-general/memory_utilisation.PNG) <br>
Throughout the load test the memory utilisation was not affected, except when a second instance was spun up and spun down. 

![response_times_general](/images/load_test/xhr-general/response_times.PNG) <br>
There was variation in the median response times, initially 70,000 ms per request, but dropping to a range of 15,000 - 40,000 ms once a second instance had been spun up. The 95 percentile followed the trends of the median response times.

![total_requests_per_second_general](/images/load_test/xhr-general/total_requests.PNG) <br>
There were roughly 4 requests per second, of which none failed.

## Launch day
We did not run a launch day load test due to the already high CPU usage of roughly 100% across the 2 instances for the general load test. 

# Conclusion
Based on our estimated usage of the dashboard, the dashboard does not fall over durin high load, however response times far exceed what would be acceptable for a good user experience, as the response time is 15,000 - 40,000 ms. At higher usage of the dashboard, such as our estimate of 460 users for launch day, we can expect poorer performance of the dashboard and larger request times, so further research needs to be carried out, to determine possible changes to allow the dashboard to support a larger number of users.

# Further Research
Upon code review, we were alerted to possible issues with using DOM element id's as part of our load test, which will cause the load test to fail if we change them and do not update the load test. We have created spike tickets to investigate how we can make the load test less prone to breaking when changes are made to the dashboard code. 

Furthermore, a ticket has been created to improve the performance of the 'Compare local authorities' page, as when running the load test we identified that the dashboard begins to struggle with a small number of simultaneous users.
