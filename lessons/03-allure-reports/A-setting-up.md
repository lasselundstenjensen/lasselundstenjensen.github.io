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

Allure for report generation is not currently offered through **`brew`** or **`pip`** or any other package managers, so we have to install it manually. Allure-behave _is_ available.

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

#### **Populating the trend chart**

```yaml
# Copy Allure categories.json definition to the reports folder before generating the HTML report
- script: |
    cp allure/categories.json reports
displayName: Set Allure report configuration

- task: AWSShellScript@1
displayName: Download existing Allure report history from S3
inputs:
    awsCredentials: ${{ variables.aws_connection }}
    regionName: ${{ parameters.aws_region }}
    scriptType: inline
    inlineScript: |
    mkdir reports/history
    aws s3 sync s3://documentation-hosting-websi-websitebucket75c24d94-1pb0fhxzv7s5y/bdd/history reports/history
    tree reports

### <generate the allure report here> ###

# Upload Allure BDD report HTML to the S3 bucket
- task: AWSShellScript@1
displayName: Upload Allure report to S3
inputs:
    awsCredentials: ${{ variables.aws_connection }}
    regionName: ${{ parameters.aws_region }}
    scriptType: inline
    inlineScript: |
    aws s3 sync reports/html s3://documentation-hosting-websi-websitebucket75c24d94-1pb0fhxzv7s5y/bdd/
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

