---
title: "Setting up shop"
description: "description"
---

## Getting Allure up and running

Allure is an open-source reporting tool that consumes automated test output and presents it to give a "test suite"-like overview.

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

#### **First an example**

Let us have a look at an existing report.

This one is generated on every development CI run.

[MLOps Allure report](https://dev.mlops.data.novonordisk.cloud/bdd/)

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

#### **Installing Allure**

`Allure` for report generation is not currently offered through **`brew`** or **`pip`** or any other package managers, so we have to install it manually. `Allure-behave` _is_ available.

The same goes for installing it as part of pipeline execution.

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

The installation consists of two parts:

<br />

- allure-behave 
  - (the formatter)
- allure 
  - (the report generator)

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

#### **Installing it all via an ADO script task**

<br />

`parameters.allure_behave_version = 2.12.0`

`parameters.allure_version = 2.20.1`

```yaml
# Install Allure and dependencies
- script: |
    python -m pip install allure-behave==${{ parameters.allure_behave_version }}
    curl -o allure-${{ parameters.allure_version }}.tgz -Ls https://github.com/allure-framework/allure2/releases/download/${{ parameters.allure_version }}/allure-${{ parameters.allure_version }}.tgz   
    sudo tar -zxvf allure-${{ parameters.allure_version }}.tgz -C /opt/   
    sudo ln -s /opt/allure-${{ parameters.allure_version }}/bin/allure /usr/bin/allure
    allure --version
displayName: Install Allure and dependencies
workingDirectory: ./
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

#### **Generate test results in the Allure format**

Before Allure can use test results to create a report, the test results need to be generated and formatted for Allure use. This is done by the allure-behave formatter.

Run tests like this:

```bash
python -W ignore -m 
  behave 
  -f allure_behave.formatter:AllureFormatter 
  -o reports
  <path to feature files directory>  
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

Here is a variation where specific tags are listed:

```bash
python -W ignore -m 
  behave 
  --tags '~@InProgress'
  -f allure_behave.formatter:AllureFormatter 
  -o reports
  <path to feature files directory>  
```

Putting a `~` in front of a tag means you are omitting tests with that tag specifically.

See [here](https://jenisys.github.io/behave.example/tutorials/tutorial11.html) for more examples.

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

#### **Generate the Allure report (HTML)**

<br />

```yaml
# Generate Allure BDD report
- script: |
    allure generate -c reports -o reports/html
displayName: Generate Allure report
workingDirectory: ./
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

#### **Running it locally is also possible**

```bash
allure open reports/html
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

#### **Populating the trend chart**

```yaml
### <run the tests here> ###
````

```yaml
- task: AWSShellScript@1
displayName: Download existing Allure report history from S3
inputs:
    awsCredentials: ${{ variables.aws_connection }}
    regionName: ${{ parameters.aws_region }}
    scriptType: inline
    inlineScript: |
    mkdir reports/history
    aws s3 sync s3://<your-bucket>/<reports>/history reports/history
```

```yaml
### <generate the allure report here> ###
````

```yaml
# Upload Allure BDD report HTML to the S3 bucket
- task: AWSShellScript@1
displayName: Upload Allure report to S3
inputs:
    awsCredentials: ${{ variables.aws_connection }}
    regionName: ${{ parameters.aws_region }}
    scriptType: inline
    inlineScript: |
    aws s3 sync reports/html s3://<your-bucket>/<reports>/
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

#### **Defining custom filters**

It is possible to define custom filters for the Allure report. This is done by creating a `categories.json` file and placing it in the root of the report folder.

A suggestion here is to create it elsewhere in the repo and copy it to the report folder before generating the report.

```json
[
  {
    "name": "Ignored tests", 
    "matchedStatuses": ["skipped"] 
  },
  {
    "name": "Broken tests",
    "matchedStatuses": ["broken"]
  },
  {
    "name": "Failed tests",
    "matchedStatuses": ["failed"]
  },
  {
    "name": "Successful tests",
    "matchedStatuses": ["passed"]
  }
]
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

More advanced examples of filtering:

<br />

```json
[
  {
    "name": "Skipped Tests By @Disabled - Definition",
    "matchedStatuses": ["skipped"],
    "messageRegex": ".*@Disabled.*"
  },
  {
    "name": "Broken Tests - Definition",
    "matchedStatuses": ["broken"],
    "traceRegex": ".*WebDriverException.*",
    "messageRegex": ".*unknown error.*"
  },
  {
    "name": "Test Defects - Definition",
    "matchedStatuses": ["broken", "failed"],
    "traceRegex": ".*NoSuchElementException.*",
    "messageRegex": ".*no such element.*"
  },
  {
    "name": "Test Defects - Definition",
    "matchedStatuses": ["broken", "failed"],
    "traceRegex": ".*IndexOutOfBoundsException.*",
    "messageRegex": "Index.*"
  },
  {
    "name": "Product Defects - Definition",
    "matchedStatuses": [ "failed" ],
    "traceRegex": ".*ElementShould.*",
    "messageRegex": ".*Element should.*"
  },
  {
    "name": "Product Defects - Definition",
    "matchedStatuses": [ "failed" ],
    "traceRegex": ".*checkCondition.*",
    "messageRegex": ".*should have.*"
  }
]
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

And to use them, they need to be copied into the test results folder, prior to generating the Allure HTML report.

<br />

```yaml
# Copy Allure categories.json definition to the reports folder before generating the HTML report
- script: |
    cp allure/categories.json /reports
displayName: Set Allure report configuration
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