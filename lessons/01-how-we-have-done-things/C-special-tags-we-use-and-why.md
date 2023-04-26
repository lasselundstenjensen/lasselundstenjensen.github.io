---
title: "Special tags we use and why"
description: "Special tags we use and why"
---

## Special tags we use and why

Some tags have been introduced to help control tests and scope. We use them to orchestrate test runs both locally and as part of the CI and deployment pipelines.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **@fixture.\<something-something\>**

We use these for pre-conditions, setup and tear-down before and after test execution. 

```gherkin
@fixture.role.validate
@fixture.s3.create_bucket
@fixture.s3.upload.test_data.vision.classification
Feature: Model evaluation can be triggered via an API
```

```gherkin
@fixture.launch.tabular.classification.app
Feature: As a data scientist, when evaluating an inference unit, then I want the evaluation report to consist
```

```gherkin
@fixture.launch.image.binary.classification.app
Feature: It must be possible to assess that an image binary classification inference unit behaves as intended
```

```gherkin
@fixture.launch.vision.classification.app
@fixture.vision.classification.inference
Feature: Evaluation plan definition
```

In this session we will not deep dive into how these work. Furthermore, Behave has specific implementation details as do other test runners.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **@offline**

Just running tests. This tag signifies a test can run offline—that is, locally.

```gherkin
@offline
...
Feature: It must be possible to assess that a tabular regression inference unit behaves as intended
```

To run all offline tests:

```bash
behave --tags '@offline' iu_validation/features
```

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **@container**

For tests that depend on a Docker container.

```gherkin
@container
Scenario: Perform end-to-end evaluation via the docker container (success)
    Given a test dataset for image binary classification
    And an evaluation plan file for image binary classification with no evaluation tasks and no report specification
    And with local report file location
    When we invoke the inference unit evaluation container with local inference
    Then the application returns a successful exit code
```

To run all container-based tests:

```bash
behave --tags '@container' iu_validation/features
```

Or leave them out:

```bash
behave --tags '~@container' iu_validation/features
```

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **@InProgress**

Flag for us to indicate a scenario is not yet complete. We can run tests with or without these scenarios.

Here is one that is commented out, because there is no feature flag for it yet:

```gherkin
#  @PV @InProgress
#  Scenario: Perform inference unit evaluation with a multiple image datapoint request to the inference unit
#    Given an inference unit utilizing multiple images for predictions
#    And a dataset in the coco dataset format supporting multiple images for each datapoint
#    When we run inference unit evaluation
#    Then we parse the responses from the inference unit as a single prediction
#    Then metrics are calculated correctly
```

To run without the tests for features/scenarios marked as _in progress_:

```bash
behave --tags '~@InProgress' iu_validation/features
```

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **@s3**
#### **@aws**
#### **@dynamodb**

<br />

Identifies tests that have something to do with _s3_, _dynamodb_ or _aws_ in general and thus dependencies to AWS cloud at runtime.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />