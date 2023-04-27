---
title: "Creating artifacts for the QMS pipeline"
description: "description"
---

## Test result need to be packaged

In order for the QMS pipeline to work and generate the verification report, the test results need to be packaged in a specific way.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **When do we generate test results**

It happens a couple of places in the pipeline:
- IV (and pIV)
- PV (and pPV)

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **What to do with them**

If they are produced inside the Azure DevOps pipeline and published as is, they should just get uploaded as a folder with test result files in it.

The most common output is a single test result file per test execution. 

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **Some context**

![](./images/qms-pipeline-creating-pv-artifacts.png)

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
