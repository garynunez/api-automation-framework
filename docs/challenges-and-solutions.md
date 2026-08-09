# Challenges & Solutions

## 1. Overview

Building the Hardware API/UI Validation Automation Framework was a hands-on learning experience. The project was developed while learning WebdriverIO and TypeScript and required solving a variety of problems involving browser automation, element interaction, synchronization, TypeScript, test architecture, and reporting.

Several of the most valuable lessons came from problems that initially prevented the framework from behaving as expected.

This document summarizes some of the major challenges encountered during development and the approaches used to solve them.

---

# 2. Learning WebdriverIO From the Ground Up

## Challenge

I had professional experience writing Python test scripts, but I had not previously worked with WebdriverIO.

The framework therefore required learning a new automation framework while simultaneously building a functional project.

This meant learning:

* WebdriverIO APIs
* Element interactions
* Browser actions
* WebDriver commands
* Asynchronous behavior
* TypeScript integration
* Mocha test organization
* Allure integration

## Approach

Rather than attempting to learn the framework independently before beginning development, I learned WebdriverIO through the actual problems encountered while building the project.

When a requirement could not be solved using a basic interaction, I investigated the underlying WebDriver capabilities and implemented the functionality required by the application.

## Result

The project became both an automation framework and a practical learning environment for TypeScript and modern browser automation.

---

# 3. Overlapping UI Elements

## Challenge

One of the application pages contained multiple elements occupying the same physical screen location.

The "Home" buttons on several pages could overlap other elements, resulting in WebDriver attempting to interact with an element that was technically present but not the intended clickable target.

This produced errors such as elements remaining non-clickable despite being located successfully.

## Investigation

Rather than immediately changing the selector, the issue was investigated through the application's HTML and element properties.

The investigation included examining:

* Multiple matching elements
* Element positions
* `z-index` values
* Visibility
* Clickability
* Which element was actually positioned above another

This revealed that the problem was related to overlapping elements and stacking order rather than simply a missing selector.

## Lesson

Finding an element is not necessarily the same as being able to interact with it.

Browser automation requires understanding the actual rendered state of the page.

---

# 4. Slider Drag Interaction

## Challenge

One of the most difficult interactions involved a slider.

A direct click on the slider track successfully changed the value, but attempting to drag the slider handle did not produce the expected movement.

The displayed slider value remained unchanged even though the automation reported that the pointer interaction had occurred.

## Investigation

The debugging process involved examining:

* Slider track coordinates
* Slider handle coordinates
* Element dimensions
* Current slider value
* Pointer position
* Pressed state
* Browser interaction behavior

The problem demonstrated that a browser-level interaction that appears correct conceptually does not necessarily reproduce the same behavior as a physical interaction.

## Approach

Lower-level WebDriver pointer actions were investigated and used to provide more precise control over:

* Pointer movement
* Pointer down
* Pointer movement while pressed
* Pointer up

This was particularly important for interactions that depend on the difference between clicking and dragging.

## Lesson

Automation sometimes requires moving beyond high-level commands and understanding the underlying browser interaction model.

---

# 5. Pointer-Based Button Events

## Challenge

Some button behaviors could not be adequately tested using a normal browser click.

The application distinguished between different physical interaction behaviors, including:

* Tapped
* Pressed
* Held
* Repeated
* Released

A simple click does not accurately represent all of these behaviors.

## Solution

The framework used lower-level pointer actions to simulate the interaction lifecycle.

Conceptually:

```text
Pointer Down
     ↓
Wait / Maintain Interaction
     ↓
Pointer Up
```

This allowed different interaction durations and sequences to be tested.

## Result

The framework could validate behavioral events rather than simply verifying that a button could be clicked.

---

# 6. WebDriver Action Errors

## Challenge

While implementing pointer interactions, the framework encountered WebDriver errors related to action payloads and missing element information.

One example involved an error indicating that the required element information was missing from a WebDriver action.

## Investigation

The problem required examining the difference between:

* WebdriverIO element objects
* WebDriver action arguments
* Element references
* Pointer action definitions

The debugging process involved determining what information the WebDriver protocol actually expected rather than assuming that any element reference could be passed directly into an action.

## Lesson

Framework abstractions can hide the underlying WebDriver protocol.

When advanced interactions fail, understanding the lower-level protocol becomes extremely valuable.

---

# 7. TypeScript Type Compatibility

## Challenge

The project encountered TypeScript errors when working with WebdriverIO element types.

One recurring issue involved differences between WebdriverIO's `ChainablePromiseElement` type and the element types expected by certain functions.

## Investigation

The issue required understanding:

* WebdriverIO's asynchronous element model
* Chainable element objects
* TypeScript type definitions
* Function parameter expectations
* When an element represents a promise-like chain versus a resolved element

## Solution

The framework code was adjusted to work with the types expected by the relevant WebdriverIO APIs rather than bypassing TypeScript's type checking.

## Lesson

Strong typing can initially make development more difficult when learning a framework, but it also exposes incorrect assumptions earlier.

Instead of discovering certain problems only at runtime, TypeScript can identify incompatible interfaces during development.

---

# 8. Synchronization and Timing

## Challenge

UI automation is highly dependent on timing.

An element may:

* Exist in the DOM
* Not yet be displayed
* Be displayed but not clickable
* Be changing state
* Be covered by another element
* Be waiting for another application action to complete

This can produce flaky or inconsistent automation.

## Solution

The framework uses reusable wait functionality through the BasePage architecture and WebdriverIO synchronization capabilities.

Instead of relying exclusively on arbitrary delays, the framework attempts to wait for the required application state.

## Lesson

Reliable UI automation requires synchronization with application behavior rather than simply adding delays until a test happens to pass.

---

# 9. Allure Reporting Problems

## Challenge

The reporting workflow did not always behave as expected after failed test executions.

At one point, an older execution remained in the Allure results and created confusion about which test execution was being displayed.

## Investigation

The issue was traced to accumulated results rather than necessarily representing the most recent test execution.

## Solution

Clearing the existing Allure results before generating a fresh report resolved the stale-result problem.

## Lesson

Test reporting systems often maintain state between executions.

When debugging reporting problems, it is important to distinguish:

> **The test execution is incorrect**

from:

> **The report is displaying previous execution data.**

This is another example of why diagnosing the source of a problem is as important as fixing the immediate symptom.

---

# 10. Test Failure vs. Product Failure

## Challenge

One of the most important challenges in QA automation is determining what a failed test actually means.

A failed automation scenario can indicate:

* Incorrect test logic
* Incorrect selector
* Synchronization issue
* Browser issue
* Environment issue
* Application defect
* API behavior problem

Treating every failure as a product defect would create inaccurate defect reports.

## Approach

When a test fails, the investigation begins with the available evidence.

This can include:

* Console output
* Test logs
* Screenshots
* Current UI state
* Expected values
* Actual values
* Application behavior
* Previous successful executions

The goal is to isolate the source of the failure before determining the appropriate action.

## Result

This approach improves the quality of defect reporting and prevents automation problems from being incorrectly attributed to the application.

---

# 11. Designing the Framework While Learning

## Challenge

The framework was not developed from an existing professional automation template that I had previously used.

I had to make architectural decisions while still learning the tools.

This created a balance between:

* Getting the automation working
* Learning the technology
* Avoiding unnecessary complexity
* Creating a structure that could support future expansion

## Approach

The architecture evolved around problems that actually appeared during development.

For example:

* Repeated browser interactions → `BasePage`
* Page-specific behavior → Page Objects
* Repeated authentication → authentication utility
* Repeated navigation → navigation utility
* Related scenarios → suite-specific specifications
* Failure investigation → screenshots and Allure

## Lesson

Good architecture does not necessarily need to be designed perfectly on day one.

It can evolve as repeated patterns and actual requirements become visible.

---

# 12. Balancing Automation With Maintainability

## Challenge

It is possible to automate almost anything, but automation that is difficult to maintain can eventually become more expensive than the manual process it replaced.

The framework therefore needed to balance automation coverage with maintainability.

## Approach

Reusable components were prioritized instead of creating isolated scripts for each scenario.

The Page Object Model helped keep selectors and interactions centralized.

Shared utilities prevented common workflows from being duplicated.

## Lesson

The goal of automation is not simply to maximize the number of automated tests.

The goal is to create automation that provides long-term value.

---

# 13. Expanding Test Coverage

## Challenge

The underlying API contains a much larger collection of potential scenarios than the initial automated coverage.

Attempting to automate everything immediately would have created a large amount of code without allowing sufficient time to validate the architecture.

## Approach

The framework was expanded incrementally.

Initial automation focused on several representative UI-element categories:

* Buttons
* Labels
* Levels
* Sliders

These areas provided different types of interactions and validation requirements.

This allowed the framework architecture to be tested against multiple types of behavior before expanding further.

## Lesson

Building a strong foundation before attempting to automate hundreds of scenarios reduces the risk of scaling a flawed architecture.

---

# 14. Working With Physical-System Behavior Through a Browser

## Challenge

The project involved a browser-based representation of a physical touch-panel environment.

This created an interesting boundary between:

```text
Physical / Hardware Behavior
          ↓
      API Behavior
          ↓
    Mirrored Web UI
          ↓
      Automation
```

The automation was not simply testing a conventional website.

It was using the browser interface as a way to interact with and validate behavior associated with the underlying system.

## Lesson

Testing systems that bridge hardware, APIs, and UI requires thinking about more than individual software components.

The test needs to validate whether the interaction produces the expected observable behavior.

---

# 15. Learning Through Debugging

Many of the most valuable improvements to the framework came from failures.

Instead of treating an error as simply something that needed to disappear, the debugging process became an opportunity to understand the underlying system.

Examples included:

* Investigating element stacking order
* Understanding pointer interactions
* Examining element coordinates
* Understanding WebDriver actions
* Resolving TypeScript type incompatibilities
* Investigating stale Allure results
* Distinguishing automation failures from application failures

Each problem expanded my understanding of browser automation and software architecture.

---

# 16. Key Problem-Solving Pattern

A consistent debugging pattern emerged throughout development:

```text
Observe Failure
      ↓
Reproduce Problem
      ↓
Collect Evidence
      ↓
Identify Possible Causes
      ↓
Test Hypothesis
      ↓
Implement Solution
      ↓
Re-run Test
      ↓
Confirm Behavior
```

This approach prevents changing code randomly until a test passes.

Instead, each change is based on evidence and a hypothesis about the underlying problem.

---

# 17. What These Challenges Taught Me

The biggest lesson from the project is that building automation is itself a software engineering problem.

The difficult parts were not simply writing:

```text
click()
```

or

```text
expect()
```

The difficult parts were determining:

* How the application actually behaves
* How browser automation represents physical interactions
* How to structure reusable code
* How to synchronize with dynamic systems
* How to diagnose failures
* How to determine whether a failure belongs to the test or the product
* How to design for future expansion

These challenges pushed the project beyond simple test scripting and into framework development.

---

# 18. Future Challenges

As the framework expands, additional engineering challenges are expected.

Potential areas include:

### Larger Test Suites

Maintaining execution time and organization as coverage grows.

### Parallel Execution

Managing shared resources and application state when multiple tests execute simultaneously.

### CI/CD

Making the framework reliable in automated pipeline environments.

### Environment Configuration

Supporting multiple environments without embedding environment-specific information into the framework.

### Test Data

Managing increasingly complex test data while maintaining test independence.

### Reporting at Scale

Maintaining useful reporting as the number of tests and executions increases.

---

# 19. Summary

The challenges encountered while developing the framework were not simply obstacles.

They became some of the most valuable parts of the project.

Each problem required understanding a different layer of the system:

* Application behavior
* Browser behavior
* WebDriver
* TypeScript
* Test architecture
* Reporting
* Debugging methodology

The result was not only a working automation framework, but also practical experience designing, debugging, and maintaining software in an environment where the correct solution was not always obvious at the beginning.

That experience reinforced an important engineering principle:

> **When something doesn't work, understand why before deciding how to fix it.**
