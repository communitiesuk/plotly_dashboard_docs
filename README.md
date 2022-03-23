# Plotly dashboard docs

**This instructions are designed to be run in the DAP environment.**

## Getting Started

### Setup your local development environment
**Note: This section only needs completing once**
1.  Set your default browser to Google Chrome - [instructions][Make Chrome your default browser].
1.  Open Anaconda Navigator via Start menu. **Note:** Anaconda asks if you want to update it, but it won't work.
1.  Install and launch VS Code (Visual Studio Code) from within the Anaconda Navigator. **Note** after installing VS Code, a Getting Started walkthrough is shown. Click the back button to escape.
1.  Navigate to the `Git CMD` from the start menu and execute the below commands. Once you have executed the commands close `Git CMD`.

    **Note: You need to change the name/email to match your own and you need to include the quotation marks. You may like to copy the commands into a word document to edit them.**

```shell
git config --global user.name "Your Name"
git config --global user.email "Your.Name@levellingup.gov.uk"
``` 

[Make Chrome your default browser]: https://support.google.com/chrome/answer/95417?hl=en-GB&co=GENIE.Platform%3DDesktop

### Downloading the code from GitHub

1.  Create a folder on your desktop, for storing source code within if you don't have one already.
1.  From VS Code open the [Explorer window][explorer_window], the overlapping pages icon on left hand menu. Select the option to "Clone Repository". Click "Clone from GitHub"
1.  If prompted, authorize with GitHub.
1.  You should be prompted to enter a repository name. Type "communitiesuk/&lt;Your repository name&gt;". Then click on communitiesuk/&lt;Your repository name&gt;.
1.  As a destination, select your folder for storing the source code. Select "Sign in with browser" if the GitHub authorisation popup is shown.
1.  This pulls the code from GitHub to your local folder.
    Click "Open folder" option, and navigate to your newly created folder containing the repository code.
1.  Select "Yes, I trust the authors".

[explorer_window]: https://code.visualstudio.com/docs/getstarted/userinterface#_explorer

### Installing packages

1.  [Open a command prompt terminal within VS Code][open-terminal], in which you'll start executing some commands. By default, the initial terminal will be a powershell terminal, and you will need to [switch to a command prompt shell][terminal-switch]. 
1.  Update the name field in environment.yml to the dashboard name
1.  Create a new conda environment by typing `conda env create -f environment.yml` into the terminal and executing the command by pressing the Enter key.
1.  Activate your conda environment with `conda activate <dashboard name> `
1. Close VS Code. Open Anaconda Navigator, select "&lt; Dashboard name &gt;" for the 'Application on' drop down menu, then select "Launch" VS Code. Click the bin icon on the terminal toolbar to close the terminal. Click the plus icon on the terminal toolbar to launch a new terminal.
1.  Install the [Microsoft Python][python_extension] extension for VS Code.
1.  Follow the [instructions for configuring the Python interpreter][configure_python_interpreter].

 **Note: If error when creating conda environment check conda version. Type 'conda --version' in VS Code terminal. conda 4.5.12 needs to be upgraded to 4.10.3. Contact DAP-support@communities.gov.uk**

[open-terminal]: https://code.visualstudio.com/docs/editor/integrated-terminal
[terminal-switch]: https://code.visualstudio.com/docs/editor/integrated-terminal#_terminal-shells
[python_extension]: https://marketplace.visualstudio.com/items?itemName=ms-python.python
[configure_python_interpreter]: https://code.visualstudio.com/docs/python/python-tutorial#_select-a-python-interpreter

### Running the application 

1.  From your VS Code terminal, execute `python run.py`
1.  Wait for the message "Dash is running on ..." message to appear
1.  Navigate to http://localhost:8080/ in your browser within the AWS workspace. Note that http://localhost:8080/ is the address that dash will run on in your local machine.
1. Use Ctrl-C in the terminal to quit the python server. 

    **Note:** Terminal can only handle one command at a time, if the python server is running the terminal will not handle any further commands. To restart the server use `python run.py`

## Development

### Running tests

Writing and running automated tests ensures software is correct, reduces risk that users will experience bugs, and allows developers to move much faster as they can make changes with confidence that any breaks will be caught be the test suite. Once you have set up unit tests:

```bash
python -u -m pytest tests
```

### Running the code formatter

The [code formatter](https://black.readthedocs.io/en/stable/) ensures source code is formatted consistently. Running the code formatter makes changes automatically that then need to be committed.

```bash
black ./
```

### Running the linter

The linter checks for basic logic errors and bugs. Linting reports rule violations that must be corrected manually.  

```bash
pylint <Dashboard name>
```

###  Add reminder message to run formatter and linter before pushing to GitHub


1.  On VS Code go to File - Preferences - Settings
1.  Search for files.exclude
1.  Mouse over "**/.git", and click the cross icon
1.  In the explorer navigate to .git/hooks/ and create a new file called pre-commit. Paste the below command into the file: 

```bash
#!/usr/bin/env bash
echo "Remember to run Black and Pylint before pushing to GitHub" 
```

### Retrieving data from CDS

1. Check data available. From within the DAP AWS Workspace, open "Microsoft SQL Server Management Studio 18".
1. Enter the Server name as "DAP-SQLTEST\CDS", and under Options specify the Database name as "Dashboards".
1. Open the "New Query" from the Toolbar, paste in the query that corresponds to your table or view and execute.
1. In the data folder of your dashboard create new file. Import pandas and pyodbc.
1. Enter code:
``` 
    conn = pyodbc.connect(
    "Driver={SQL Server};"
    "Server=DAP-SQLTEST\CDS;"
    "Database=Dashboards;"
    "Trusted_Connection=yes;"
)
sql_query = pd.read_sql_query(
    """ 
    [ENTER SQL QUERY HERE]
    """,
    conn,
)
```
1. Save as dataframe. Convert to csv. 

SQL cheat sheet available [here](https://learnsql.com/blog/sql-basics-cheat-sheet/)

For more information on accessing CDS you can find some more [detailed instructions][cds-documentation] from Mark Foster.

[export-headers]: https://solutioncenter.apexsql.com/sql-server-management-studio-ssms-how-to-save-results-with-headers/
[cds-documentation]: https://mhclg.sharepoint.com/:w:/r/sites/AnalyticalDashboards/_layouts/15/Doc.aspx?action=edit&sourcedoc=%7BC32BC170-2D98-4128-AD31-63FEFEFF0E0D%7D

## Deployment

### Staging

Code pushed to GitHub on the `main` branch will be automatically deployed to a staging environment once you've set deployment to [Gov PaaS](https://www.cloud.service.gov.uk/) and the GitHub Actions (see below), and will be accessible at the following link:

> https://&lt;name in manifest.yml&gt;.london.cloudapps.digital/

### Production

1.  From VS Code navigate to File -> Open Folder and select `Q:\AnalyticalDashboards\<your dashboard name>` 
1.  Pull the latest code from GitHub into that directory.
1.  Switch back to your local development environment.
1.  Send an email to DAP-Support@communities.gov.uk from your Communities email address, asking for the dashboard to be updated.  Sample message:
```
Dear DAP support,

Please could you update the Analytical Data Dashboard at
<your production URL>

With the code in Q:\AnalyticalDashboards\<your dashboard name>
```

1. Notify the Development teams channel that a request has been made.
    The request should take less than 1 working day to be completed.

## Troubleshooting

### My DAP environment seems to be broken.  How can I get it working again?

Refresh the environment by running this command:

```bash
conda env update -f environment.yml
```

If this doesn't resolve the problem, you can try destroying and recreating your environment.
1. Deactivate your conda environment with `conda deactivate`
1. Remove the existing environment with `conda env remove --name <Dashboard name>`
1. Run the environment setup steps in [Installing packages](#installing-packages) again.
