# Assumptions

We have used [Werkzeug](https://community.plotly.com/t/performance-profiling-dash-apps-with-werkzeug/65199), a performance profiling dash app, to identify the areas of the code which are running slowly. 


# Findings
We found that the functions from our code which tended to be slow, were those which manipulated the dataframes. However, the slowest parts of the app overall were the dash components themselves and the generation of charts within our callbacks. 

# Conclusion
Based on our findings, we identified four possible solutions, ranked in prioritisation order of time taken to update:

**Example of time taken on update after changing LA:**

- compare_local_authorities - 1.46 s
- create_jitter_components - 1.25 s / 80%
- add_style_to_benchmarking_fig - 44% of create_jitter_components
- create_single_group_jitter_figure - 32% of create_jitter_components
- create_la_summary_commentary - 13% of create_jitter_components

1. **[Find the optimum number of gunicorn workers for the dashboard hosted in gov pass:](https://trello.com/c/MdyxBrGX/680-wip-gunicorn-settings)**
- We are not specifying a number of workers in our current gunicorn deployment, so a default of 1 is being applied. Gunicorn recommends the formula **(2 x $num_cores) + 1** to be used when calculating the number of workers. We will run load tests whilst using 1, 2 and 3 workers and determine which performs best.
- This is highest priority as it requires a small config change and could improve the number of users each app instance can support.

2. **[Investigate updating LA jitter points rather than re-generating jitter charts when LA's are selected:](https://trello.com/c/VJAIYcsF/682-spike-investigate-updating-la-jitter-points-rather-than-re-generating-jitter-charts-when-las-are-selected)** 
- Investigate if it is possible to only update selected local authority points on jitter charts, instead of regenerating all the jitter charts each time an LA is selected.
- This is second priority as it could make big imporvements to the plot generation times, however we are unsure if this functionality can be implemented and if so how much effort it would require.

3. **[Improve caching of dataframes inside the dashboard:](https://trello.com/c/RcK4F2Pk/679-wip-caching-of-dataframes)** 
- Move the retrieval of the data frames into a central location and load data frames from there instead of passing them into functions as arguments, means you can more easily cache expensive functions. Currently the data frames can be cached, but the functions manipulating the data in the data frames can't be cached as they receive the data frame as an argument to the function.
- Advantages: 
  - runs on load of server, first call to get data frame won’t be slow
  - lazy loading not that beneficial for data frames which we know will be used
  - functions which have a data frame passed in as an argument can’t be cached as the caching decorator needs arguments to be serializable. By the data frames being imported into a file, you can cache more easily. (For example create_la_summary_commentary)
- Disadvantages: 
  - need to be careful not to modify the data frames anywhere, would be good if we could make them immutable
- This is lowest priority as it wouldn't impact the plot generation times, which is where most of the time is taken.

4. **Plotly client side performance improvements:**
- Update callbacks to be [clientside callbacks](https://dash.plotly.com/clientside-callbacks) so that functions are ran on the client's machines rather than the server.
- After research, we decided this solution was not suitable as it involved re-writing callbacks to use JavaScript, which would be time consuming, add complexity to the code and require DLUHC upskilling.
