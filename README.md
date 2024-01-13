# Machine learning system design patterns

This repository contains the material for the book: [Machine learning system design patterns](https://mrparosk.github.io/).

## Topics

- Common training workflow structure
- Decoupling feature creation from training & serving workflow
- Data validation for inference
    - Batch inference
    - Online inference
    - Distribution skew versus drift
    - Metrics to determine distribution skew / drift
- Embed pre & post processing logic in the model
    - Embedded in the model object
    - Separate task of the pipeline (batch-inference) (TODO)
- Online serving structure
- Model registry
    - Handling dependencies (TODO)
- Infrastructure structure
    - Sandbox, staging & production (TODO)
    - Promoting between staging & production (TODO)

## Render the Markdown

In order to render the markdown to HTML first install mdbook & mdbook-mermaid package:

```console
cargo install mdbook
cargo install mdbook-mermaid
```

To build the book, run:

```console
mdbook-mermaid install
mdbook build
```

To continually serve the book, run:

```console
mdbook serve
```
