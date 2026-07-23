<div align="center">

<img src="https://raw.githubusercontent.com/deepset-ai/haystack/main/images/banner.png" alt="Haystack banner" width="100%">

<br>

<h1>Haystack</h1>

<h3>The open-source AI orchestration framework for production-ready RAG and agents</h3>

<p>
  <a href="https://pypi.org/project/haystack-ai/"><img src="https://img.shields.io/pypi/v/haystack-ai?color=F2A93B&label=PyPI" alt="PyPI"></a>
  <img src="https://img.shields.io/pypi/pyversions/haystack-ai?color=1B2A4A&logo=python&logoColor=gold" alt="Python versions">
  <a href="LICENSE"><img src="https://img.shields.io/github/license/deepset-ai/haystack?color=1B2A4A" alt="License"></a>
  <a href="https://github.com/deepset-ai/haystack/actions/workflows/tests.yml"><img src="https://github.com/deepset-ai/haystack/actions/workflows/tests.yml/badge.svg" alt="Tests"></a>
  <a href="https://docs.haystack.deepset.ai"><img src="https://img.shields.io/website?label=docs&up_message=online&url=https%3A%2F%2Fdocs.haystack.deepset.ai&color=F2A93B" alt="Docs"></a>
  <a href="https://discord.com/invite/qZxjM4bAHU"><img src="https://img.shields.io/discord/993534733298450452?logo=discord&color=1B2A4A&logoColor=white" alt="Discord"></a>
</p>

<p>
  <a href="https://haystack.deepset.ai/"><b>Website</b></a> ·
  <a href="https://docs.haystack.deepset.ai"><b>Documentation</b></a> ·
  <a href="https://haystack.deepset.ai/tutorials"><b>Tutorials</b></a> ·
  <a href="https://haystack.deepset.ai/cookbook"><b>Cookbook</b></a> ·
  <a href="https://discord.com/invite/qZxjM4bAHU"><b>Discord</b></a>
</p>

</div>

<br>

## What is Haystack?

Haystack is a Python framework for building LLM-powered applications you can actually put into production — retrieval-augmented generation (RAG), semantic search, question answering, and autonomous agents.

Instead of hiding logic behind magic abstractions, Haystack gives you **explicit, composable building blocks** — components you connect into pipelines and agent workflows. You decide exactly how retrieval, ranking, memory, tool use, and generation fit together, and you can swap any piece out without rewriting the rest of the system.

<br>

<div align="center">
<img src="images/pipeline-flow.svg" alt="A Haystack pipeline: Documents to Retriever to Prompt Builder to LLM Generator to Answer" width="100%">
</div>

<br>

## Why Haystack

<table>
<tr>
<td width="50%" valign="top">

### 🤖 Production-first agents
Lifecycle hooks (`before_llm`, `before_tool`, `on_exit`, …) give you guardrails and custom logic. Step counts, token usage, and tool calls are tracked out of the box.

### 🧩 Context engineering, not prompt guesswork
Precise control over how information is retrieved, ranked, filtered, and routed before it ever reaches the model.

### ⚡ Native async & streaming
The same `Pipeline` runs sync or async and streams tokens as they're generated. `Agent` can run tool calls concurrently.

</td>
<td width="50%" valign="top">

### 🔧 Modular by design
Use the built-in components or write your own, and wire them together with loops, branches, and conditional logic.

### 🌐 Model- and vendor-agnostic
OpenAI, Anthropic, Mistral, Cohere, Hugging Face, Google, Azure OpenAI, AWS Bedrock, local models — all behind one consistent interface.

### 🧱 Extensible ecosystem
A shared component interface makes it easy to build and share integrations with the community.

</td>
</tr>
</table>

<br>

## The ecosystem

<div align="center">
<img src="images/ecosystem-overview.svg" alt="Haystack ecosystem: LLM providers, document stores, agents and tools, retrieval and ranking, community integrations, and deployment options, all around the Haystack core" width="100%">
</div>

<br>

## Installation

```bash
pip install haystack-ai
```

Want the bleeding edge?

```bash
pip install --pre haystack-ai
```

Docker images and other installation methods are covered in the [installation docs](https://docs.haystack.deepset.ai/docs/installation).

<br>

## Quick example

A minimal RAG pipeline: retrieve relevant documents, then generate an answer grounded in them.

```python
from haystack import Pipeline, Document
from haystack.document_stores.in_memory import InMemoryDocumentStore
from haystack.components.retrievers.in_memory import InMemoryBM25Retriever
from haystack.components.builders import ChatPromptBuilder
from haystack.components.generators.chat import OpenAIChatGenerator
from haystack.dataclasses import ChatMessage

# 1. Store some documents
document_store = InMemoryDocumentStore()
document_store.write_documents([
    Document(content="Haystack is an open-source AI orchestration framework."),
    Document(content="Pipelines connect components like retrievers and generators."),
])

# 2. Build a pipeline
pipeline = Pipeline()
pipeline.add_component("retriever", InMemoryBM25Retriever(document_store=document_store))
pipeline.add_component("prompt_builder", ChatPromptBuilder(
    template=[ChatMessage.from_user(
        "Given these documents: {{documents}}\nAnswer the question: {{question}}"
    )]
))
pipeline.add_component("llm", OpenAIChatGenerator(model="gpt-4o-mini"))

pipeline.connect("retriever.documents", "prompt_builder.documents")
pipeline.connect("prompt_builder.prompt", "llm.messages")

# 3. Run it
result = pipeline.run({
    "retriever": {"query": "What is Haystack?"},
    "prompt_builder": {"question": "What is Haystack?"},
})
print(result["llm"]["replies"][0].text)
```

> New to Haystack? Start with [**"What is Haystack?"**](https://haystack.deepset.ai/overview/intro) and the [**Get Started guide**](https://haystack.deepset.ai/overview/quick-start), then explore the [tutorials](https://haystack.deepset.ai/tutorials) and [Cookbook](https://haystack.deepset.ai/cookbook) for real-world recipes.

<br>

## Deploying pipelines as APIs

Want to serve your pipelines and agents as **REST APIs** or **MCP servers**? [Hayhooks](https://github.com/deepset-ai/hayhooks) wraps them with custom logic and exposes them over HTTP or MCP, including OpenAI-compatible chat completion endpoints that work with UIs like [open-webui](https://openwebui.com/).

<br>

## Documentation

| | |
|---|---|
| 📖 | [Documentation](https://docs.haystack.deepset.ai/docs/intro) |
| 🎓 | [Tutorials](https://haystack.deepset.ai/tutorials) |
| 🍳 | [Cookbook](https://haystack.deepset.ai/cookbook) — real-world recipes and advanced use cases |
| 📰 | [Blog](https://haystack.deepset.ai/blog) |

<br>

## Telemetry

Haystack collects **anonymous** usage statistics about which components are initialized, so the maintainers know what matters most to the community. No personal data or content ever leaves your machine. Read more, or opt out, in the [telemetry docs](https://docs.haystack.deepset.ai/docs/telemetry).

<br>

## Community

- 🐛 Found a bug or have a feature request? [Open an issue](https://github.com/deepset-ai/haystack/issues).
- 💬 Want to discuss an idea or get advice? [GitHub Discussions](https://github.com/deepset-ai/haystack/discussions) or the [Discord server](https://discord.com/invite/qZxjM4bAHU).
- 🐦 Follow along on [X (Twitter)](https://twitter.com/haystack_ai) or ask on [Stack Overflow](https://stackoverflow.com/questions/tagged/haystack).

<br>

## Contributing

Contributions of all sizes are welcome — from a typo fix to a brand-new feature. Read the [Contributor Guidelines](CONTRIBUTING.md) to get started, and check the [list of issues open for contribution](https://github.com/orgs/deepset-ai/projects/14).

You can contribute to:
- the core [Haystack](https://github.com/deepset-ai/haystack) project
- an integration in [haystack-core-integrations](https://github.com/deepset-ai/haystack-core-integrations)
- the [documentation site](https://github.com/deepset-ai/haystack/tree/main/docs-website)

<br>

## License

Haystack is released under the [Apache License 2.0](LICENSE).

## Citation

If you use Haystack in your research, please cite it using the metadata in [`CITATION.cff`](CITATION.cff).

