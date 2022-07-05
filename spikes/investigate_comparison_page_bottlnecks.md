# Assumptions

We have used [Werkzeug](https://community.plotly.com/t/performance-profiling-dash-apps-with-werkzeug/65199), a performance profiling dash app, to identify the areas of the code which are running slowly. 


# Findings
We found that the functions from our code which tended to be slow, were those which manipulated the dataframes. However, the slowest parts of the app overall were the dash components themselves and the generation of charts within our callbacks. 


# Conclusion
Based on our findings, we identified four possible solutions:

1. **[Improve caching of dataframes inside the dashboard:](https://trello.com/c/RcK4F2Pk/679-wip-caching-of-dataframes)** 
- Move the retrieval of the data frames into a central location and load data frames from there instead of passing them into functions as arguments, means you can more easily cache expensive functions.
- Advantages: 
  - runs on load of server, first call to get df won’t be slow
  - lazy loading not that beneficial for data frames which we know will be used
  - functions which have a data frame passed in as an argument can’t be cached as the caching decorator needs arguments to be serializable. By the data frames being imported into a file, you can cache more easily
- Disadvantages: 
  - need to be careful not to modify the data frames anywhere, would be good if we could make them immutable
 
2. **[Find the optimum number of gunicorn workers for the dashboard hosted in gov pass:](https://trello.com/c/MdyxBrGX/680-wip-gunicorn-settings)**
- We are not specifying a number of workers in our current gunicorn deployment, so a default of 1 is being applied. Gunicorn recommends the formula **(2 x $num_cores) + 1** to be used when calculating the number of workers. We will run load tests whilst using 1, 2 and 3 workers and determine which performs best.

3. **[Investigate updating LA jitter points rather than re-generating jitter charts when LA's are selected:](https://trello.com/c/VJAIYcsF/682-spike-investigate-updating-la-jitter-points-rather-than-re-generating-jitter-charts-when-las-are-selected)** 
- Investigate if it is possible to only update selected local authority points on jitter charts, instead of regenerating all the jitter charts each time an LA is selected.

4. **Plotly client side performance improvements:**
- Update callbacks to be [clientside callbacks](https://dash.plotly.com/clientside-callbacks) so that functions are ran on the client's machines rather than the server.
- After research, we decided this solution was not suitable as it involved re-writing callbacks to use JavaScript, which would be time consuming, add complexity to the code and require DLUHC upskilling.
