---
layout: section
---

# Ralph is an ETL

---

## The `convert` (sub)command

```sh
Usage: ralph convert [OPTIONS]

  Converts input events to a given format.

Options:
  From edX to xAPI converter options:
    -u, --uuid-namespace TEXT     The UUID namespace to use for the `ID` field
                                  generation
    -p, --platform-url TEXT       The `actor.account.homePage` to use in the
                                  xAPI statements  [required]
  -f, --from [edx]                Input events format to convert  [required]
  -t, --to [xapi]                 Output events format  [required]
  -I, --ignore-errors             Continue writing regardless of raised errors
  -F, --fail-on-unknown           Stop converting at first unknown event
  --help                          Show this message and exit.
```

---
layout: image-right
image: /images/fonzie-yeah.jpg
---

## ETL: Extract Transform Load

Fetch Open edX events wrapped in a zipped GELF archive, convert it to xAPI and
feed the elasticsearch data lake with it.

```sh
ralph fetch --backend swift | \
    gunzip | \
    ralph extract --parser gelf | \
    ralph convert \
      --from edx \
      --to xapi \
      --platform-url "https://www.fun-mooc.fr" | \
    ralph push --backend es
```

---
layout: two-cols
---

## Pydantic models

```python
# ralph/models/edx/navigational/statements.py
class UIPageClose(BaseBrowserModel):
    """Represents edx `page_close` browser statement."""
    __selector__ = selector(
        event_source="browser",
        event_type="page_close"
    )

    event: Literal["{}"]
    event_type: Literal["page_close"]
    name: Literal["page_close"]

# ralph/models/xapi/navigation/statements.py
class PageTerminated(BaseXapiModel):
    """Represents a page terminated xAPI statement."""
    __selector__ = selector(
        object__definition__type="http://activitystrea.ms/schema/1.0/page",
        verb__id="http://adlnet.gov/expapi/verbs/terminated",
    )

    object: PageObjectField
    verb: TerminatedVerbField = TerminatedVerbField()
```

::right::

## Converter

```python
# ralph/models/edx/converters/xapi/navigational.py
class UIPageCloseToPageTerminated(BaseXapiConverter):
    """Converts a common edX `page_close` event to xAPI.

    Example Statement:

    John terminated https://www.fun-mooc.fr/ page.
    """

    __src__ = UIPageClose
    __dest__ = PageTerminated

    def _get_conversion_items(self):
        """Returns a set of ConversionItems used for
        conversion.
        """

        conversion_items = super()._get_conversion_items()
        return conversion_items.union(
            {
                ConversionItem("object__id", "page")
            }
        )
```
