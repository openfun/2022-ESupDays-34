---
layout: section
---

# Ralph is a CLI

---
layout: image-right
image: /images/ralph-fonzie.jpg
---

## Installation

Ralph is distributed as python package:

```sh
# Create a new virtualenv (optional)
$ python -m venv venv
$ source venv/bin/activate

# Install the package (in a virtualenv)
(venv) $ pip install ralph-malph
```

... and a Docker image:

```sh
$ docker run --rm -i fundocker/ralph:latest \
    ralph --help
```

---

## Usage

As `git` or `docker`, `ralph` implements (sub)commands:

```
Usage: ralph [OPTIONS] COMMAND [ARGS]...

  Ralph is a stream-based tool to play with your logs.

Options:
  -v, --verbosity LVL  Either CRITICAL, ERROR, WARNING, INFO (default) or
                       DEBUG
  --help               Show this message and exit.

Commands:
  convert    Converts input events to a given format.
  extract    Extracts input events from a container format using a...
  fetch      Fetch an archive or records from a configured backend.
  list       List available archives from a configured storage backend.
  push       Push an archive to a configured backend.
  runserver  Runs the API server for the development environment.
  validate   Validates input events of given format.
```

---
layout: image-right
image: /images/ralph-bubblegum.jpg
---

## Example workflows

Push a local archive to MongoDB:

```sh
zcat 20220923-lms-statements.jsonl.gz | \
  ralph push --backend mongo
```

Import new data from a LDP[^1] stream:

```sh
ralph list --backend ldp --new |
  xargs -I {} -n 1 bash -c "
    ralph fetch --backend ldp {} |
    gunzip |
    ralph extract --parser gelf |
    ralph push --backend es"
```

[^1]: [Log Data Platform](https://www.ovhcloud.com/fr/logs-data-platform/) (OVH's Graylog)

---
layout: image-right
image: /images/ralph-mic.jpg
---

## Quickly explore your data

Get the top-100 accounts that have generated the most events:

```sh
ralph fetch --backend swift 20220923.xapi.gz | \
    jq .actor.account.name | \
    sort | \
    uniq -c | \
    sort -rn | \
    head -n 100
```
---
layout: image-right
image: /images/ralph-potsie-cards.jpg
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

[^1]: [Pull request](https://github.com/openfun/ralph/pull/213) in review
