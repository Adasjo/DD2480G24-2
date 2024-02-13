# DD2480G24: CI

This project contains a functioning implementation of a CI server which communicates through the GitHub API and GitHub Webhooks to build the project (tests and syntax checking). The server stores all build outputs  which are accessible as secure endpoints on the server. Upon a new pull request, the server sets a pending status of the commit on GitHub, builds the project, and provides results as well as sets the status depending on the result.

## Folder structure

The following main folders and files constitute the project:
- DD2480G24-2
    - `docs` Containts build information
    - `src`
        - `build` Containts method executed upon a build request
        - `dummycode` Contains code which can be used to test the CI implementation
        - `logs` Contains all log files for builds
        - `utils` Contains utility functions such as test runner and syntax checker
        - `app.py` Contains main flask application
        - `payload.py` Contains Webhook parser
    - `tests` Contains all tests of the CI server
    - `Makefile` Build commands for local development
    - `README.md`
    - `requirements.txt` Contains all required modules
    - `SERVER.md` Contains information about the host ssh server
    - `SPEC.md` Contains a specification of the CI server


## Testing
The unit testing is based on the Python built in `unittest` framework (https://docs.python.org/3/library/unittest.html)

To run all tests in a file:
- `python -m unittest <path to testfile>`

## API Endpoints

### Build
- `GET /build`: Endpoint used by the GitHub Webhook to build the project upon a pull request
### Logs
- `GET /logs/<commit_id>`: Returns the logs for the given commit id
- `GET /logs/all`: Returns all logs

The logs are stored in the `logs` directory and are named after the commit id. The logs are stored in the format `logs/<commit_id>.log`. Based on this we can retrieve the logs for a specific commit id. This endpoint will return a dict with the date of the build, the commit id and the log messages.

The `GET /logs/all` endpoint will return a list of all logs in the logs directory.

### Build
- `POST /build`: Endpoint used by the GitHub Webhook to build the project upon a pull request

The build endpoint is used by the GitHub Webhook to trigger a build of the project. The endpoint will be triggered on all commits to the repository. The endpoint will compile the project by running a syntax check and running the tests. The results of the build will be stored in the logs directory. The endpoint will also update the status of the commit on GitHub.

## GitHub Webhooks
This implementation utilises several webhooks for different purposes, such as handling issue creation and pull requests. 
Currently, the CI server implementation is hosted locally and consequently all internet traffic is tunneled through [ngrok](https://ngrok.com). Any given Webhook in this project has the following characteristics:
- `Payload URL`: The forwarding URL provided by `ngrok`
- `Secret`: Secret message for validation of payload authenticity
- `Content type`: application/json
- `SSL verification`: Enable SSL verification
- `Events`: The event handled by the Webhook

## GitHub API
The GitHub API is used to set the status of a commit during the CI process on the server. 
The implementation requires that the environment variables `BUILD_SECRET` and `GITHUB_TOKEN`
are set, as these are required to verify GitHub webhooks as well as make requests to the
GitHub API. The method `set_status` in `run_build.py` is used to set the status of a commit to either of the
available statuses "pending", "success", "error" or "failure". The implementation follows the 
[GitHub documentation](https://docs.github.com/en/rest/commits/statuses?apiVersion=2022-11-28) under section `Create a commit status`.

## Documentation
Adding New Modules to Sphinx Documentation
To include new modules in the Sphinx documentation, use the following command:

```bash
sphinx-apidoc -o docs/source/ src/
```

This command generates reStructuredText files in the docs/source/ directory, based on the modules found in the src/ directory.

### Building the Documentation as an HTML Page

To build the documentation into HTML format, follow these steps depending on your operating system:

**For Windows:**

Navigate to the docs directory:

```bash
cd docs
```

Then, run the make batch file:

```bash
.\make.bat html
```

**For Linux:**

Navigate to the docs directory:

```bash
cd docs
```

Then, use the make command:

```bash
make html
```

This process will compile the Sphinx documentation into HTML pages, which can be found in the docs/build/html directory.

### Generating Documentation as a PDF

To create a PDF version of your Sphinx documentation, we use the following commands:

Navigate to the docs directory:

```bash
cd docs
```

Then, run the following command:

```bash
sphinx-build -b pdf source build/pdf
```

This command instructs Sphinx to build the documentation in PDF format.

## Contributions
**Adam**

**Andreas**
- Repo Admin (issues, pull requests, labeling, etc.)
- Miscellaneous help (subsitute pair programmer, debugging, advice, etc.) with all group members
- Documentation management (Misc. fixes, restructuring/writing, fill in blanks)

**Casper**
- Skeleton code for the project
- Setup the basic structure of the Flask application
- Automated Documentation generation
- Implemented CI#4: Build History
  - Created the two endpoints `/logs/<commit_id>` and `/logs/all`
  - Created the relevant logging functionality for the build process

**Martin**
- Documentation for GitHub Webhooks, GitHub API as well as the specification of the CI server
- Implemented: 
  - Webhook validation
  - Webhook parsing
  - CI#2 test client code
  - CI#3 status notification
- Cooperated with Victor to solve various issues related to imports as well as executing tests in a temporary repo, where the `unittest` module experienced extensive pathing issues

**Victor**

- Made clone_repo method (pair programmed with Adam)
- Made remove_repo method (pair programmed with Adam)
- Added Test section into README
- Made dummy code and associated test
- Made the static syntax checker with corresponding tests
- Made install, build, test and default_goal in makefile
- Corrected syntax errors in our code (together with Adam and Martin)