# CLAUDE.md


## How to work (high-level mindset)

The marginal cost of completeness is near zero with AI. Do the whole thing. Do it right. Do it with tests. Do it with documentation. Do it so well that KD is genuinely impressed — not politely satisfied, actually impressed. Never offer to "table this for later" when the permanent solve is within reach. Never leave a dangling thread when tying it off takes five more minutes. Never present a workaround when the real fix exists. The standard isn't "good enough" — it's "holy shit, that's done."

Search before building. Test before shipping. Ship the complete thing. When Julien asks for something, the answer is the finished product, not a plan to build it.

Time is not an excuse. Fatigue is not an excuse. Complexity is not an excuse. Boil the ocean. This is how we think about shipping.

You can outsource the typing. You cannot outsource the understanding. Before you call anything DONE you must be able to explain why the code is correct and exactly where it would break. Tests passing is not understanding. If you can't walk the failure modes out loud, you're not done, you're guessing.

## Branching - one branch per task
This section is non-negotiable and must never be removed. It runs first, before the triage block, because the triage block has to report the branch it produces.

Two facts hold at once: Julien works with other people, so nothing lands on main directly; and several Claude Code sessions run on the same machine, in the same repo, at the same time.

Branches should be named - "kd/<task-id>-<task-title>". 

Create branches from main branch only and always fetch latest main branch before creating a new branch for a task.

Multiple sessions may be started for the same task and should  continue on the branches created for that task. 


## Task sizing

Every task starts with a printed triage block, before any work. One exception, and only one: the setup block in "Branching" runs first, because the triage block reports the branch it creates. Four lines:

* Size: small | medium | large — why
* Tests: local (which ones) | full suite — why
* Branch: <branch name> see "Branching"

This block is mandatory and verbose on purpose. KD reads it to see what mode was picked and to tune these rules over time. A wrong mode is only correctable if the choice is visible. Never skip it, never bury it mid-report. The Branch line is there so that with several sessions running at once, KD can tell at a glance which one is about to touch what.


## Completion status protocol
At the end of every task, report one of:

* DONE — All steps completed. Evidence provided for every claim with tests + evals. Ready to merge.
* DONE_WITH_CONCERNS — Completed, but with issues Julien should know about. List each concern with severity and a proposed follow-up.
* BLOCKED — Cannot proceed. State what's blocking and what was already tried.
* NEEDS_CONTEXT — Missing information required to continue. State exactly what's needed.

"Partially done" is not a status. Either the feature ships (DONE) or it doesn't (BLOCKED / NEEDS_CONTEXT). Honesty about incompleteness beats pretending.


## Safety
* Never edit secrets.
* Never commit secrets. If .env is touched, verify .gitignore before any commit.
* Never skip pre-commit hooks with --no-verify. If a hook fails, fix the underlying issue.


## Development flow
- Define the task
- Create the branch
- Implement the change
- Commit the change to the branch
- Open a pull request


## Conventions

- Use the latest supported UiPath Studio version defined for the project.
- Workflows must follow the REFramework pattern unless there is a documented exception.
- Prefer reusable workflows and libraries over duplicated logic.
- Keep business logic separated from application interaction logic.
- Externalize all configurable values into Config files, Assets, Orchestrator queues, or input arguments.
- Avoid hardcoded values, file paths, credentials, URLs, and environment-specific settings.
- Use Orchestrator Assets and Credential Assets for all secrets and credentials.
- Variables, arguments, workflows, queues, assets, and folders must follow consistent naming conventions.
- Use meaningful workflow and variable names that clearly describe their purpose.
- All workflows must include a concise description in the workflow annotations.
- Every activity requiring explanation should include a brief comment describing its purpose.
- Complex business rules must include comments explaining the logic and expected outcome.
- Logging must be implemented at all major process milestones, transactions, exceptions, and decision points.
- Use structured logging whenever possible.
- Exception handling must be implemented explicitly. Do not suppress exceptions unless documented and justified.
- All changes must be documented in `CHANGELOG.md` following the Keep a Changelog convention.
- Follow UiPath and RPA best practices for performance, reliability, maintainability, and reusability.

## Development Principles

### Reusability

- Create reusable components for common actions.
- Avoid duplicate workflows and repeated sequences.
- Reusable components should be parameterized using arguments.

### Maintainability

- Keep workflows small and focused on a single responsibility.
- Break large workflows into multiple reusable workflows.
- Minimize nesting depth where possible.
- Use descriptive display names for all activities.

### Reliability

- Implement retry logic where appropriate.
- Use explicit timeout values for selectors, applications, and web interactions.
- Validate application state before performing actions.
- Add appropriate validation after critical actions.

### Security

- Never store credentials in workflows, project files, code, or comments.
- Use Orchestrator Assets or approved enterprise secret management solutions.
- Remove sensitive information from logs.
- Follow organizational security policies for data handling and storage.

## Logging Standards

- Log process start and completion.
- Log transaction start and completion.
- Log major business decisions.
- Log exceptions with sufficient context for troubleshooting.
- Do not log passwords, tokens, credentials, or sensitive customer information.
- Use appropriate log levels:
  - Trace: Detailed debugging information.
  - Info: Normal process execution.
  - Warn: Recoverable issues.
  - Error: Process failures and exceptions.
  - Fatal: Unrecoverable process termination.

## Testing Requirements

- Test all workflows before committing changes.
- Validate all business scenarios, exception scenarios, and edge cases.
- Perform end-to-end testing prior to release.
- Document any known limitations or assumptions.
- Verify successful execution in the target environment before deployment.

## Project Structure

```text
Main.xaml                    Primary process entry point

Framework/
    InitAllSettings.xaml     Configuration initialization
    InitAllApplications.xaml Application initialization
    GetTransactionData.xaml  Transaction retrieval
    Process.xaml             Transaction processing
    EndProcess.xaml          Cleanup and shutdown

Workflows/
    Business/                Business logic workflows
    Applications/            Application interaction workflows
    Utilities/               Shared utility workflows
    Validation/              Validation workflows

Data/
    Config.xlsx              Process configuration
    Input/                   Input files
    Output/                  Generated output files

Tests/
    Test cases
    Test data

Documentation/
    Process design documents
    Solution design documents
    Run books

CHANGELOG.md
README.md
```

## Pull Request Requirements

- Verify all workflows execute successfully.
- Verify no credentials, secrets, or sensitive data are included.
- Verify all warnings and validation issues have been addressed.
- Update documentation when functionality changes.
- Update `CHANGELOG.md` for user-facing or operational changes.
- Include testing evidence in the pull request description.
- Justify any new dependencies, packages, or external integrations.

## Automation Standards

- Prefer Modern Experience activities unless a project requirement dictates otherwise.
- Use reliable selectors and avoid brittle UI automation patterns.
- Use Object Repository where applicable.
- Use Simulate Click, Simulate Type, and Background Processing when appropriate.
- Minimize use of delays; prefer application state validation.
- Avoid Send Hotkey and image-based automation unless no reliable alternative exists.
- Document all exceptions to established standards.
