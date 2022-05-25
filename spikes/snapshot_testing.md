# Context

We have investigated whether [Percy](https://percy.io/), an automated visual testing tool, works with our local government dashboard. The dash testing package provides this functionality through the use of dash-duo.

```python
from index import app

def test(dash_duo):
    dash_duo.start_server(app, 8085)
    # ... load the page to take a snapshot of
    dash_duo.percy_snapshot("test_snapshot_01")
```

Visual testing tools ensure the presentation of the page is consistent when updates are being made. A baseline image can be set, which can be used to compare future images to - any changes are then highlighted in red within the Percy user interface. These changes can then be reviewed, and if deemed appropriate a new baseline image can be set for future screenshots to be compared against. Otherwise, the difference is a highlighted regression, which can be addressed quicker. Our dashboard mostly displays data in graphs/charts, which are produced in Plotly using SVG’s, which are difficult to test through code alone.

A test was performed on the income dashboard page, which included taking Percy snapshots to investigate how we use this tool.

# Findings

We performed a Percy visual test on the income dashboard page. We first created a baseline image, then made changes to the data and re-ran the Percy test - the data changes were highlighted in red. However, the screenshots produced by Percy did not display the x-axis, y-axis and title labels where expected.

![percy_snapshot_example](../images/percy-snapshots/percy_snapshot_example.png)
![percy_snapshot_diff_example](../images/percy-snapshots/percy_snapshot_diff_example.png)

Our original thinking was that this was due to a sizing issue with the screenshot. So we trialled using various different widths, all of which appeared the same. Another theory we had was that the page hadn't finished loading by the time the screenshot was taken, so a wait over different periods of time between the screen load and a snapshot being taken, ranging between 1 and 10 seconds was trialled, again there was no improvement.

# Conclusion

Based on our attempts to use Percy and the timebox placed on this spike, we do not recommend the use of Percy without further investigation. Furthermore, there are alternative visual testing tools available, such as [Playwright](https://playwright.dev/python/docs/intro) which is Python based and [Image diff (created for uk trade)](https://github.com/uktrade/cypress-image-diff) which uses Cypress.

# Further research 

Based on some quick research, we believe Playwright could be the better option to test next. This is due to it using Python, a technology we are already familiar with compared to Cypress, it is well documented and uses the same underlying technology - [Pixelmatch](https://github.com/mapbox/pixelmatch).
