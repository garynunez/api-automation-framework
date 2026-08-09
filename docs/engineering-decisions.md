# Engineering Decisions

## 1. Overview

Building the Hardware API/UI Validation Automation Framework required making decisions about programming language, automation tooling, architecture, test organization, reporting, and maintainability.

These decisions were made with two primary goals:

1. Solve the immediate need for repeatable automated validation.
2. Create a foundation that could continue growing as additional test coverage was added.

This document describes the major engineering decisions made during development and the reasoning behind them.

---

# 2. Choosing TypeScript

## Decision

The framework was developed using **TypeScript**.

## Context

Python was already the primary programming language used in my professional QA work. However, I intentionally chose TypeScript for this automation project.

The decision was influenced by my desire to expand my software engineering skill set and gain practical experience with a strongly typed language.

I had also learned that future products within the organization were expected to make greater use of TypeScript, making the project an opportunity to develop a skill that could be valuable beyond the immediate automation effort.

## Reasoning

Using TypeScript allowed me to:

* Develop experience with a new programming language.
* Work within a strongly typed environment.
* Strengthen object-oriented programming skills.
* Gain practical experience with modern JavaScript/TypeScript tooling.
* Expand beyond my existing Python experience.

## Trade-off

Using a language I was less familiar with increased the learning curve.

However, the additional learning was intentional and provided value beyond simply completing the automation task.

---

# 3. Choosing WebdriverIO

## Decision

The framework uses **WebdriverIO** for browser automation.

## Context

The system being tested provided a browser-based interface that mirrored the touch-panel environment.

This made browser automation a practical way to interact with the UI elements while validating the behavior produced by the underlying system.

## Reasoning

WebdriverIO provided:

* Browser automation
* Element interaction
* Explicit synchronization capabilities
* Pointer actions
* Integration with TypeScript
* Integration with Mocha
* Integration with Allure reporting
* A flexible automation API

The framework also required interactions beyond basic clicking, including pointer-based actions for validating button events.

WebdriverIO provided the lower-level interaction capabilities necessary for those scenarios.

## Trade-off

Browser automation introduces additional dependencies on:

* Browser state
* Timing
* Element availability
* Environment configuration

These issues required careful synchronization and failure handling.

---

# 4. Choosing the Page Object Model

## Decision

The framework uses the **Page Object Model (POM)**.

## Context

As test coverage increased, placing selectors and browser interactions directly inside every test would have created duplicated and difficult-to-maintain code.

The Page Object Model provided a way to separate test behavior from UI implementation.

## Reasoning

The architecture allows specifications to focus on:

> **What behavior is being validated?**

while Page Objects handle:

> **How the application is interacted with.**

For example, a test can conceptually request that a slider be set to a specific value without needing to know which selector represents the slider or how the browser should interact with it.

## Benefits

* Reduced duplication
* Improved readability
* Centralized selectors
* Easier maintenance
* Reusable interactions
* Easier expansion

## Trade-off

Page Objects introduce another abstraction layer.

For a very small automation project, this may add unnecessary complexity.

For this project, however, the expected growth in test coverage justified the additional structure.

---

# 5. Creating a BasePage

## Decision

Common browser interactions were centralized within a `BasePage`.

## Context

Multiple Page Objects require common operations such as:

* Waiting for elements
* Clicking
* Setting values
* Retrieving text
* Waiting for conditions

Implementing these operations separately in every Page Object would create unnecessary duplication.

## Reasoning

Centralizing these operations provides one reusable interaction layer.

Conceptually:

```text id="0y7t3f"
             BasePage
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
   HomePage  ButtonsPage  SlidersPage
```

If a common browser interaction needs to change, the shared implementation can be updated rather than modifying every Page Object individually.

## Trade-off

A BasePage can become a "god class" if too much unrelated functionality is placed inside it.

For that reason, the BasePage should remain focused on genuinely common browser interaction behavior.

---

# 6. Separating Test Specifications From Page Objects

## Decision

Test scenarios and page interaction logic were kept in separate files.

## Structure

```text id="z8w8j1"
pageobjects/
specs/
utils/
```

## Reasoning

This separation makes the intent of the test easier to understand.

A developer reviewing a specification should primarily see:

```text id="yn7y2k"
Arrange
Act
Validate
```

rather than a large collection of selectors and low-level browser commands.

This also makes maintenance easier because changes to the application's UI can generally be isolated to the relevant Page Object.

---

# 7. Creating Shared Utilities

## Decision

Authentication and navigation were extracted into shared utilities.

## Context

Multiple test suites require common workflows such as authentication and navigation.

Duplicating these workflows inside each test suite would increase maintenance effort.

## Reasoning

The utilities provide a centralized location for functionality shared across multiple test areas.

Current shared utilities include:

* Authentication
* Navigation

The authentication workflow also checks whether authentication is already required before attempting to log in.

This avoids unnecessary login operations when the application is already authenticated.

---

# 8. Choosing Allure Reporting

## Decision

The framework uses **Allure** for test reporting.

## Context

Console output alone provides limited information when a larger automated regression suite fails.

A reporting system provides a more structured way to review execution results.

## Reasoning

Allure provides:

* Organized test results
* Test suite visibility
* Pass/fail information
* Failure details
* Execution information
* Screenshot attachments

This makes the results easier to review after a full regression run.

---

# 9. Automatic Screenshot Capture

## Decision

The framework captures screenshots when tests fail.

## Context

A textual error message does not always provide enough information to understand the state of the application at the time of failure.

## Reasoning

A screenshot can provide immediate visual context.

For example, it can help determine whether:

* The wrong page was displayed.
* An element was missing.
* An unexpected value appeared.
* The UI was in an unexpected state.

The screenshot is then incorporated into the Allure reporting workflow.

---

# 10. Retry Configuration

## Decision

The framework includes test retry behavior.

## Context

UI automation can occasionally encounter transient failures caused by timing, browser state, or environmental conditions.

## Reasoning

Retries provide an opportunity to determine whether a failure is reproducible.

A transient failure that passes on retry may indicate an environmental or synchronization problem, while a consistently failing scenario warrants deeper investigation.

## Important Consideration

Retries are not intended to hide legitimate defects.

A test that repeatedly fails should still be investigated.

---

# 11. Suite-Based Test Execution

## Decision

The framework supports both full execution and individual test-suite execution.

## Current Suites

```text id="nq0j22"
Buttons
Labels
Levels
Sliders
```

## Reasoning

During development, running every test after every change can unnecessarily increase feedback time.

Suite-specific execution allows focused development and debugging.

Full execution remains available for broader regression validation.

This provides a balance between:

**Fast development feedback**

and

**Comprehensive regression coverage.**

---

# 12. Choosing npm Scripts for Execution

## Decision

The framework uses npm scripts to provide standardized commands for test execution and reporting.

The project includes commands for:

* Running the complete test suite
* Running individual suites
* Generating Allure results
* Opening Allure reports
* Combining execution and reporting workflows

## Reasoning

Standardized commands make the framework easier to operate.

Instead of requiring users to remember complex command-line arguments, common workflows can be represented by simple project commands.

This also establishes a foundation for eventually integrating the framework into CI/CD systems.

---

# 13. Designing for Reusability

## Decision

Reusable functionality was prioritized over creating isolated scripts.

## Context

The framework was intended to grow beyond the initial collection of automated scenarios.

Creating a separate script for every test would result in significant duplication.

## Reasoning

Reusable Page Objects and utilities allow additional scenarios to take advantage of functionality that already exists.

For example, once navigation and authentication functionality has been implemented, additional test suites can reuse it rather than implementing another version.

---

# 14. Designing for Future Expansion

## Decision

The framework was structured with future test coverage in mind.

## Context

The API contains a substantially larger collection of test scenarios than the initial automated coverage.

The framework therefore needed to be capable of expanding without requiring a complete rewrite.

## Reasoning

The combination of:

* Page Objects
* BasePage
* Shared utilities
* Modular specifications
* Standardized execution
* Centralized reporting

provides a foundation for adding additional coverage incrementally.

---

# 15. Choosing Simplicity Over Premature Abstraction

## Decision

The framework intentionally avoids introducing unnecessary abstraction before it provides value.

## Reasoning

Every abstraction adds complexity.

A framework can become difficult to understand if it introduces layers simply because they are considered "best practice" without solving an actual problem.

The current architecture therefore focuses on abstractions that address real needs:

* BasePage → shared browser interactions
* Page Objects → page-specific behavior
* Utilities → shared workflows
* Specifications → test behavior

As the project grows, additional abstractions can be introduced when repeated patterns justify them.

---

# 16. Manual Testing vs. Automation

## Decision

Automate repetitive, deterministic validation while retaining human investigation for failures and ambiguous behavior.

## Reasoning

Automation is particularly valuable when a test:

* Is executed frequently.
* Produces predictable results.
* Requires repetitive interaction.
* Has clear expected outcomes.
* Benefits from regression coverage.

Human investigation remains important when determining whether a failure represents:

* An automation defect
* A synchronization problem
* An environment problem
* A product defect

The framework therefore supports QA rather than attempting to eliminate human reasoning from the process.

---

# 17. Engineering Trade-offs

Several trade-offs were considered throughout development.

### Learning vs. Speed

TypeScript increased the initial learning curve but provided valuable software engineering experience.

### Abstraction vs. Simplicity

The Page Object Model and BasePage add structure but reduce duplication and improve maintainability.

### Automation vs. Reliability

Automation reduces manual effort but introduces dependencies on browser state and synchronization.

### Retry vs. Transparency

Retries can reduce transient failures but must not conceal legitimate defects.

### Current Needs vs. Future Scalability

The framework was designed to support future growth without introducing unnecessary complexity before it was needed.

---

# 18. What I Would Improve With More Time

As the framework continues to mature, several areas could be improved.

### Environment Configuration

Move environment-specific information into external configuration or environment variables.

### Test Data

Introduce structured test data management for scenarios requiring larger or more varied data sets.

### Parallelization

Explore parallel execution as the number of automated scenarios increases.

### CI/CD

Integrate automated execution into the software development pipeline.

### Reporting

Expand reporting with historical trends, failure categorization, and execution metrics.

### Additional Abstraction

Extract additional reusable components only when repeated patterns demonstrate that the abstraction provides measurable value.

---

# 19. Engineering Perspective

The most important lesson from these decisions is that framework development is not simply about selecting popular technologies.

Each technology and architectural pattern should solve a problem.

The framework was therefore built around the following principle:

> **Choose the simplest design that solves the current problem while leaving room for the system to evolve.**

This approach allowed the framework to begin as a practical solution to repetitive manual testing while developing into a reusable automation architecture.

---

# 20. Summary

The major engineering decisions behind the framework were driven by maintainability, reusability, scalability, and practical value.

The resulting architecture combines:

* TypeScript
* WebdriverIO
* Page Object Model
* BasePage
* Shared utilities
* Modular specifications
* npm execution scripts
* Retry handling
* Screenshot capture
* Allure reporting

Together, these decisions provide a foundation for continued automation growth while keeping the framework understandable and maintainable.

More importantly, developing the framework provided practical experience in making engineering decisions, evaluating trade-offs, learning unfamiliar technologies, and designing software intended to solve a real-world problem.
