# Ralph, the ultimate toolbox for your learning analytics

Ralph is an open source project developed by France Université Numérique. It
has been designed as a CLI (and a LRS server) to handle data streams with
learning analytics in mind.

This python-powered tool has been designed to build data workflows as UNIX
pipelines with the KISS (Keep It Small and Simple) mantra as a credo: each
sub-command achieves a simple task and their combination with other command
line tools (_e.g._ `ag`, `jq`, `xargs`, ...) performs complex treatments.

During this talk, we will introduce Ralph features and its possible
integration in an infrastructure used to collect, transform and store
learning traces.

## Getting started

Install dependencies:

```
$ yarn install
```

Run the development server:

```
$ yarn run slidev
```

Visit: http://localhost:3030

Learn more about Slidev on [documentations](https://sli.dev/).

## License

This work is released under the MIT License (see [LICENSE](./LICENSE.md)).
