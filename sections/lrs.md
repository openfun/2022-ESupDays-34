---
layout: section
---

# Ralph is a LRS

---
layout: image-right
image: /images/ralph-coke.jpg
---

## Learning Record Store (LRS)

> The LRS, as defined by the [xAPI
> specification](https://github.com/adlnet/xAPI-Spec/blob/master/xAPI-About.md#def-learning-record-store),
> is “a server (i.e. system capable of receiving and processing web requests)
> that is responsible for receiving, storing, and providing access to Learning
> Records.”

🎉 Ralph bundles a [FastAPI](https://fastapi.tiangolo.com)-based lightweight
LRS that handles the `/xAPI/statements` endpoint with `POST` and `GET` requests
to receive and return xAPI statements.

🎉 Elasticsearch and MongoDB backends are supported.

🎉 Implements statements forwarding for complex workflows.

---
layout: image-right
image: /images/ralph-surprise.gif
---


## Let's play!

Run the development server for testing:

```sh
ralph runserver --backend es
```

The LRS server should be up and running on the 8100 port, ready to receive xAPI
statements:

```sh
$ zcat 20220923.xapi.gz | \
    jq -s . | \
    http \
      --json \
      --auth julien:password \
      POST \
      http://localhost:8100/xAPI/statements/

HTTP/1.1 200 OK
content-length: 1951
content-type: application/json
date: Sun, 25 Sep 2022 20:43:56 GMT
server: uvicorn
```
