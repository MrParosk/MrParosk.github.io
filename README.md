# Machine learning system design patterns

This repository contains the material for the book: [Machine learning system design patterns](https://mrparosk.github.io/).

## Topics

- Common training workflow structure
- Decoupling feature creation from training & serving workflow
- Data validation for inference
    - Batch inference
    - Online inference
    - Train-serving skew (TODO)
    - Drifts (TODO)
- Embed pre & post processing logic in the model
    - Embedded in the model object
    - Separate task of the pipeline (batch-inference) (TODO)
- Online serving structure
- Model registry
    - Handling dependencies (TODO)

## Render the Markdown

In order to render the markdown to HTML first install mdbook & mdbook-mermaid package:

```console
cargo install mdbook
cargo install mdbook-mermaid
```

To build the book, run:

```console
mdbook build
mdbook-mermaid install
```

To continually serve the book, run:

```console
mdbook serve
```
