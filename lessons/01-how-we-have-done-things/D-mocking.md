---
title: "Mocking"
description: "Mocking"
---

## Mocking

<br />
<br />

#### **Mock the right things**

We use mocking for primarily two reasons:

<br />

1. speed up testing
2. have a degree of immutability

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

With that said, we have mainly mocked things that would be time consuming to test in the cloud because of service deployment and provisioning time.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

We still do integration tests, but not all the time. The mocks are there to help us go faster in local development.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **Designing for testability**

Building your stuff so it is testable is important. It is not always easy, but it is worth it.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

Something like this:

![](./images/testable-component-design-ecb-pattern.png)

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **We have several test applications**

![](./images/test-apps.png)

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

An example of the tabular regression app:

```python
import http
import json
import pathlib
import sys
import traceback

from pydantic import ValidationError

from iu_validation.evaluation.integrations.pydantic_models import Response, Request, IUResultRegression

ROOT_DIR = str(pathlib.Path(__file__).parents[2])
sys.path.insert(0, ROOT_DIR)

import uvicorn

import argparse
import pickle
import os.path

from iu_validation.utils.util import test_models_path
from iu_validation.test_apps.util import (
    read_body,
    get_ping_response_context,
    get_response_context_from_response,
    get_validation_error_response_context,
    get_exception_response_context,
    get_resource_not_found_response_context,
    send_response,
)

MODEL = pickle.load(open(os.path.join(test_models_path(), "tabular_regression_model.pkl"), "rb"))

MODEL_INPUT_FIELDS = [
    "area",
    "bedrooms",
    "bathrooms",
    "stories",
    "guestroom",
    "basement",
    "parking",
    "areaperbedroom",
    "bbratio",
]


async def app(scope, receive, send):
    path = scope["path"]
    if path == "/api/ping":
        response_context = get_ping_response_context()
    elif path == "/api/predict":
        try:
            request_body = json.loads(await read_body(receive))
            request = Request(**request_body)
            response = predict(request)
            response_context = get_response_context_from_response(response)
        except ValidationError as exception:
            response_context = get_validation_error_response_context(exception)
        except Exception as exception:
            traceback.print_exc()
            response_context = get_exception_response_context(exception)
    else:
        response_context = get_resource_not_found_response_context(path)

    await send_response(response_context, send)


def predict(request: Request) -> Response:
    request_dict = request.examples[0]
    model_input = [[request_dict[input_field] for input_field in MODEL_INPUT_FIELDS]]
    prediction = MODEL.predict(model_input)
    return Response(result=[IUResultRegression(output=str(round(prediction[0], 2)))])


if __name__ == "__main__":
    argumentParser = argparse.ArgumentParser(
        description="Test application for exercising tabular inference unit validation"
    )
    argumentParser.add_argument(
        "port",
        type=int,
    )
    arguments = argumentParser.parse_args()
    uvicorn.run("tabular_regression:app", port=arguments.port, log_level="info")
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

#### **Mocking it**

Using Behave as orchestrator for the test runs, they call the IUV docker container, which in turn calls the mock application that behaves like a simulated real deployed AI  model.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

Super high-level, it looks like this:

![](./images/mock-app-construction.png)

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />