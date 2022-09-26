---
layout: section
---

# Ralph is a library

---
layout: two-cols
---

## Use xAPI models library


```json
// Front-end event payload
{
  "actor": {
    "objectType": "Agent",
    "account": {
      "name": "John Doe",
      "homePage": "http://fun-mooc.fr"
    }
  },
  "verb": {
    "id": "https://w3id.org/xapi/video/verbs/played"
  },
  "object": {
    "definition": {
      "type": "https://w3id.org/xapi/video/activity-type/video"
    },
    "id": "uuid://4484f345-6711-5aea-a0d6-42a5004c879f",
    "objectType": "Activity"
  }
  /* [...] */
}
```

::right::

```python
# Backend
import uuid
import requests

from ralph.models.xapi.video.statements import VideoPlayed
from . import app

@app.post("/events/", status_code=201)
def post(event: str) -> uuid.UUID:
    """Validate front-end event and add identifier field
    if missing.
    """

    # Parse and validate event JSON string
    statement = VideoPlayed.parse_raw(event)
    statement.id = uuid.uuid4() if statement.id is None

    # Send event to an LRS
    response = requests.post(
        "https://lrs.org/xAPI/statements/",
        data=statement.dict()
    )

    return response.json()
```

---

## Use builtin converters

```python
from ralph.models.converter import ConversionItem
from ralph.models.edx.server import Server
from ralph.models.xapi.navigation.statements import PageViewed

from .base import BaseXapiConverter

class ServerEventToPageViewed(BaseXapiConverter):
    """Converts a common edX server event to xAPI.
    Example Statement: John viewed https://www.fun-mooc.fr/ page.
    """
    __src__ = Server
    __dest__ = PageViewed

    def _get_conversion_items(self):
        """Returns a set of ConversionItems used for conversion."""
        conversion_items = super()._get_conversion_items()
        return conversion_items.union(
            {
                ConversionItem(
                    "object__id",
                    "event_type",
                    lambda event_type: self.platform_url + event_type,
                ),
            }
        )
```

---

## Generate test fixtures

Use [Hypothesis](https://hypothesis.readthedocs.io) with Pydantic models to
generate fixtures for your tests:

```python
from ralph.models.xapi.navigation.statements import PageTerminated
from tests.fixtures.hypothesis_strategies import custom_given


@custom_given(PageTerminated)
def test_models_xapi_page_terminated_statement(statement):
    """Tests that a page_terminated statement has the expected verb.id and
    object.definition.
    """

    assert statement.verb.id == "http://adlnet.gov/expapi/verbs/terminated"
    assert statement.object.definition.type == "http://activitystrea.ms/schema/1.0/page"
```

---
layout: two-cols
---

## Multiple backends, same API

```python
# ralph.backends.database.base module
from abc import ABC, abstractmethod

class BaseDatabase(ABC):
    """Base database backend interface."""
    name = "base"
    query_model = BaseQuery

    @abstractmethod
    @enforce_query_checks
    def get(
        self,
        query: BaseQuery = None,
        chunk_size: int = 10
    ):
        pass

    @abstractmethod
    def put(
        self,
        stream: Union[BinaryIO, TextIO],
        chunk_size: int = 10,
        ignore_errors: bool = False,
    ) -> int:
        pass
```

::right::

```python
    @abstractmethod
    def query_statements(
        self,
        params: StatementParameters
    ) -> StatementQueryResult:
        """Returns the statements query payload using xAPI
        parameters.
        """

    @abstractmethod
    def query_statements_by_ids(
        self,
        ids: list[str]
    ) -> list:
        """Returns the list of matching statement IDs from
        the database.
        """

```

---
layout: image-right
image: https://i.pinimg.com/originals/3d/a5/17/3da5173d53f14664222defa4befdf773.jpg
---

## Supported backends

* Database
    - Elasticsearch
    - MongoDB
* Storage:
    - Swift
    - AWS S3[^1]
    - LDP (Log Data Platform)
* Stream
    - WebSocket

[^1]: Pull request in review
