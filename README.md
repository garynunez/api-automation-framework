
# API/UI Validation Automation Framework

A modular UI automation framework built with **TypeScript** and **WebdriverIO** to automate validation of hardware API-driven behavior through a browser-based interface.

The framework was designed to replace repetitive manual validation with maintainable, reusable automated tests while providing structured reporting and failure diagnostics.

> **Note:** This repository is a sanitized portfolio representation of a professional automation project. Proprietary source code, internal URLs, credentials, product-specific identifiers, and confidential implementation details are intentionally excluded.

---

## 🚀 Overview

This project demonstrates the design and development of a browser-based automation framework for validating hardware-related software behavior.

The framework interacts with a web interface that mirrors a physical touch-panel environment. Automated tests perform UI actions and validate the resulting state, labels, values, and events against expected behavior.

The framework is organized around the **Page Object Model (POM)** and separates test specifications, page interaction logic, and shared utilities.

Test execution is integrated with **Allure Reporting**, including automatic screenshot capture when tests fail.

---

## 🎯 Problem Statement

The original validation process required extensive manual interaction with the system.

A typical validation workflow involved:

1. Launching the required application/interface.
2. Logging into the system.
3. Navigating to the appropriate test page.
4. Performing UI interactions.
5. Observing the resulting hardware/software behavior.
6. Comparing the observed result with the expected result.
7. Repeating the process across numerous test cases.
8. Documenting failures.

As the number of API and hardware scenarios increased, this process became increasingly repetitive and time-consuming.

The goal of this project was to create a reusable automation framework capable of executing these scenarios consistently while producing useful diagnostic information when failures occurred.

---

# 🏗 Architecture

The framework is organized into three primary layers:

### Page Objects

Page objects encapsulate UI elements and interaction methods for each application page.

Current page objects include:

* `BasePage`
* `LoginPage`
* `HomePage`
* `ButtonsPage`
* `LabelsPage`
* `LevelsPage`
* `SlidersPage`

The `BasePage` provides reusable operations such as:

* Waiting for elements
* Clicking elements
* Setting values
* Retrieving text
* Waiting for custom conditions

This keeps common browser interactions centralized rather than duplicated throughout individual tests.

---

### Test Specifications

Test specifications describe the behavior being validated.

Current suites include:

* Buttons
* Labels
* Levels
* Sliders

The specifications focus on expected behavior rather than low-level browser implementation details.

---

### Shared Utilities

Reusable utilities provide functionality that spans multiple test suites.

Current utilities include:

* Authentication
* Navigation

The authentication utility also checks whether the application is already authenticated before performing the login workflow, preventing unnecessary repeated authentication.

---

# 🔄 Test Execution Flow

```text
                 ┌─────────────────────┐
                 │   Test Specification│
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │     Page Object     │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │   Shared Utilities  │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Browser / Web UI    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Expected Behavior   │
                 │      Validation     │
                 └──────────┬──────────┘
                            │
                    ┌───────┴────────┐
                    │                │
                  PASS              FAIL
                    │                │
                    │                ▼
                    │       ┌─────────────────┐
                    │       │ Screenshot      │
                    │       │ Capture         │
                    │       └────────┬────────┘
                    │                │
                    └───────┬────────┘
                            ▼
                 ┌─────────────────────┐
                 │   Allure Report     │
                 └─────────────────────┘
```

---

# 🧪 Current Test Coverage

The uploaded project currently contains **21 active test scenarios** distributed across four UI-element suites.

Additional scenarios are present in the codebase as commented-out development or future test cases.

## Buttons

Current scenarios validate:

* State changes
* Pressed events
* Tapped events
* Held events
* Repeated events
* Released events
* Enable/disable behavior
* Visibility behavior

The framework also contains support for pointer-based interaction using WebDriver actions to validate event behavior that cannot be reliably represented by a simple click.

---

## Labels

Current scenarios validate:

* Text modification
* Visibility changes

---

## Levels

Current scenarios validate:

* Visibility
* Increment behavior
* Decrement behavior
* Setting a specific value
* Changing the supported range
* Step/increment behavior after range changes

---

## Sliders

Current scenarios validate:

* Enable/disable behavior
* Visibility
* Setting a specific value
* Range configuration
* Moving the slider to the top and bottom of its range

---

# 📊 Reporting

The framework integrates **Allure Reporting** to provide structured test results.

The reporting workflow supports:

* Test result generation
* Suite-specific reports
* Test execution steps
* Failure diagnostics
* Automatic screenshots for failed tests
* Report generation and opening

When a test fails, the framework captures a screenshot and attaches it to the corresponding Allure result.

This makes failures significantly easier to investigate than relying solely on console output.

---

# ⚙️ Technology Stack

### Languages

* TypeScript

### Automation

* WebdriverIO
* WebDriver
* Mocha

### Reporting

* Allure

### Architecture

* Page Object Model
* Reusable utility layer
* Shared base-page functionality

### Development Tools

* Visual Studio Code
* Git
* GitHub
* npm

---

# 🧠 Engineering Decisions

## Why TypeScript?

The framework was intentionally developed using TypeScript rather than relying solely on an existing Python-based testing environment.

This provided an opportunity to develop practical experience with a strongly typed language while aligning the project with TypeScript-based technologies being adopted within the organization.

---

## Why WebdriverIO?

WebdriverIO provided the browser automation capabilities needed to interact with the mirrored touch-panel interface while allowing the framework to remain fully integrated with the TypeScript ecosystem.

It also provided access to lower-level browser interaction capabilities required for more advanced scenarios.

---

## Why Page Object Model?

The Page Object Model was selected to separate:

**What the test is validating**

from

**How the application is interacted with.**

For example, a test can express an action such as:

```text
Set slider to 50
Verify slider value is 50
```

while the implementation details remain inside `SlidersPage`.

This improves maintainability and makes future expansion easier.

---

## Why a Base Page?

Common browser operations are centralized within `BasePage`.

Examples include:

* Waiting for elements
* Clicking
* Setting values
* Retrieving text
* Waiting for custom conditions

This reduces duplicated synchronization logic and creates a consistent interaction layer for individual page objects.

---

## Why Shared Authentication and Navigation Utilities?

Authentication and navigation are used across multiple test suites.

Moving these responsibilities into shared utilities prevents each suite from implementing its own version of the same workflow.

The authentication utility also determines whether the application is already logged in before initiating a new login sequence.

---

# 🛠 Challenges & Engineering Lessons

Building this framework introduced several challenges that required developing knowledge beyond basic browser automation.

## Browser Interaction

Some hardware-style interactions could not be accurately represented using simple clicks.

Pointer actions were introduced to simulate press and release behavior and validate event states such as:

* Pressed
* Held
* Released
* Repeated

---

## Synchronization

The application contains dynamic UI behavior, making timing and synchronization important.

The framework uses explicit waits and condition-based synchronization to reduce flaky interactions.

---

## Reusable Architecture

As the number of tests increased, it became increasingly important to avoid placing implementation details directly inside test specifications.

The Page Object Model and shared utilities allowed the framework to grow without duplicating the same browser interaction logic across every test.

---

## Debugging Automation Failures

Automation failures can originate from several different sources:

* Test logic
* Element selectors
* Browser interaction
* Application behavior
* Timing
* Environment issues

Developing the framework required learning to distinguish between automation defects and defects in the system being tested.

---

# 📈 Results

The professional implementation of this framework significantly reduced the amount of time required for manual regression validation.

The manual process previously required approximately **four hours** of repetitive validation.

The automated workflow reduced the execution time to approximately **90 seconds** for the automated regression scenario.

### Impact

```text
Manual Validation

~4 hours
      │
      ▼
Automated Validation

~90 seconds
```

The framework also provides:

* Repeatable execution
* Automated validation
* Failure screenshots
* Structured reporting
* Suite-level execution
* Reusable test components
* A foundation for additional API/hardware scenarios

---

# 📈 Scalability

The framework was designed with expansion in mind.

The current implementation contains four major UI-element suites:

```text
Buttons
Labels
Levels
Sliders
```

The same architecture can be extended as additional hardware/API scenarios are automated.

The long-term objective is to expand automation coverage across a much larger collection of API validation scenarios while maintaining a consistent architecture.

---

# 🔮 Future Development

Potential future improvements include:

* Expand automated API/hardware coverage
* Increase test scenario coverage
* Integrate the Python automation launcher
* Improve test data management
* Introduce CI/CD execution
* Support parallel test execution
* Improve environment configuration
* Expand reporting and historical trend analysis
* Add additional diagnostic information to failed tests
* Further abstract reusable browser interactions

---

# 📚 Lessons Learned

Building this framework has been one of my most valuable software engineering learning experiences.

I began the project without previous professional experience with WebdriverIO or TypeScript.

Through the development process, I gained practical experience with:

* TypeScript
* WebdriverIO
* Page Object Model architecture
* Browser automation
* WebDriver actions
* Asynchronous programming
* Test architecture
* Reusable software components
* Automated reporting
* Debugging
* Framework design
* Maintainability
* Software engineering trade-offs

More importantly, the project reinforced the value of designing software for the future rather than only solving the immediate problem.

---

# 🎓 Skills Demonstrated

This project demonstrates practical experience with:

* TypeScript
* WebdriverIO
* Test Automation
* Page Object Model
* API/UI Validation
* Browser Automation
* Object-Oriented Programming
* Software Architecture
* Debugging
* Automated Reporting
* Allure
* Git
* npm
* Mocha
* Agile Development

---

# 👨‍💻 About the Developer

I'm Gary Nuñez, a Computer Science graduate and Software Quality Engineer focused on transitioning into Software Engineering.

I enjoy building software, solving difficult technical problems, and creating automation that eliminates repetitive work.

My current areas of focus include:

* Backend development
* Python
* Java
* TypeScript
* Software architecture
* Automation frameworks

This project represents one of the major steps in my transition from software quality engineering toward software engineering.

---

# 🔗 Connect

**GitHub:**
https://github.com/garynunez

**LinkedIn:**
https://linkedin.com/in/garynunez

**Portfolio:**
Coming soon

---

## Project Status

**Status:** Active / Continuing Development

This repository represents a sanitized engineering case study and will continue evolving as the underlying automation project and my software engineering skills develop.
