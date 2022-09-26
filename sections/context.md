---
layout: section
---

# Context

---
layout: full
class: text-center
---

## How it started


```mermaid
flowchart TB
    openedx(Open edX - LMS) -- edX --> graylog
    openedx -- edX --> swift
    subgraph legacy
    swift[(Swift)]
    ll(Learning Locker - LRS) -- xAPI --> mongo[(MongoDB)]
    end
    marsha(Marsha - Video) -- xAPI --> graylog[(Graylog)]
    marsha -- xAPI --> ll
    ashley(Ashley - Forum) -- xAPI --> graylog
```

---
layout: image-right
image: /images/a-team.jpg
---

## The plan

<div class="text-center text-4xl">
    <logos-python /> &nbsp;
    <span>+</span> &nbsp;
    <logos-elasticsearch />
</div>


* Setup an [Elasticsearch](https://www.elastic.co/elasticsearch/)-based **data lake** dedicated to learning analytics
* Develop a [Python](https://python.org) tool able to **fetch** & **transform**  data from various backends


---
layout: full
class: text-center
---

## How it's going (1)

```mermaid
flowchart LR
    subgraph legacy
    swift[(Swift)]
    mongo[(MongoDB)]
    end
    ralph(Ralph) -- xAPI --> es[(Elasticsearch)]
    mongo -- xAPI --> ralph
    swift -- edX --> ralph
```

---
layout: full
class: text-center
---

## How it's going (2)

```mermaid
flowchart LR
    openedx(Open edX - LMS) -- edX --> graylog
    marsha(Marsha - Video) -- xAPI --> graylog
    ashley(Ashley - Forum) -- xAPI --> graylog
    graylog[(Graylog)] -- GELF --> ralph(Ralph) -- xAPI --> es[(Elasticsearch)]
```
