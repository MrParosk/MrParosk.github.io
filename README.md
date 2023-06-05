# Machine learning system design patterns

## Topics

- Common training workflow structure
- Decouple feature creation
- Data validation
    - Batch inference
    - Online inference
    - Train-serving skew
    - Drifts
- Online serving
    - Embed pre & post-process in the model
    - Separate services for pre-process, model inference and post-processing
- Pre-processing logic
    - Embedded in the model object
    - Separate task of the pipeline
- Model registry
    - Handling dependencies

## Render the Markdown

In order to render the markdown to HTML first install mdbook & mdbook-mermaid package:

```console
cargo install mdbook
cargo install mdbook-mermaid
```

Then run:

```console
mdbook serve
```
