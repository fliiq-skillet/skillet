# Fliiq Skillet 🍳

**Skillet** is an HTTP-native, OpenAPI-first framework for packaging and running
reusable *skills* (micro-functions) that Large-Language-Model agents can call
over the network.

> "Cook up a skill in minutes, serve it over HTTPS, remix it in a workflow."

---

## Why another spec?

Current community standard **MCP** servers are great for quick sandbox demos
inside an LLM playground, but painful when you try to ship real-world agent
workflows:

| MCP Pain Point | Skillet Solution |
| -------------- | ---------------- |
| Default **stdio** transport; requires local pipes and custom RPC          | **Pure HTTP + JSON** with an auto-generated OpenAPI contract |
| One bespoke server **per repo**; Docker mandatory                         | Single-file **Skillfile.yaml** → deploy to Cloudflare Workers, AWS Lambda or raw FastAPI |
| No discovery or function manifest                                         | Registry + `/openapi.json` enable automatic client stubs & OpenAI function-calling |
| Heavy cold-start if each agent needs its own container                    | Skills are tiny (≤ 5 MB) Workers; scale-to-zero is instant |
| Secrets baked into code                                                   | Standard `.skillet.env` + runtime injection |
| Steep learning curve for non-infra devs                                   | `pip install fliiq-skillet` → `skillet new hello_world` → **done** |

### Key Concepts

* **Skilletfile.yaml ≤50 lines** — declarative inputs/outputs, runtime & entry-point
* **`skillet dev`** — hot-reload FastAPI stub for local testing
* **`skillet deploy`** — one-command deploy to Workers/Lambda (more targets soon)
* **Registry** — browse, star and import skills; share community "recipes" in the *Cookbook*
* **Cookbook** — visual builder that chains skills into agent workflows

### Quick start (Python)

```bash
pip install fliiq-skillet
skillet new fetch_html --runtime python
cd fetch_html
skillet dev          # Swagger UI on http://127.0.0.1:8000
```

## Examples

The `examples/` directory contains reference implementations of Skillet skills:

- [anthropic_fetch](examples/anthropic_fetch/README.md) - Fetches HTML content from URLs. A Skillet-compatible implementation of the Anthropic `fetch` MCP.
- [anthropic_time](examples/anthropic_time/README.md) - Returns the current time in any timezone. A Skillet-compatible implementation of the Anthropic `time` MCP.
- [anthropic_memory](examples/anthropic_memory/README.md) - A stateful skill that provides a simple in-memory key-value store. A Skillet-compatible implementation of the Anthropic `memory` MCP.

Each example includes:
- A complete `Skilletfile.yaml` configuration
- API documentation and usage examples
- An automated `test.sh` script to verify functionality

### Testing the Examples Using the Automated Test Scripts

To test any example, you'll need two terminal windows:

1. First terminal - Start the server:
```bash
cd examples/[example_name]  # e.g., anthropic_fetch, anthropic_time, anthropic_memory
pip install -r requirements.txt
uvicorn skillet_runtime:app --reload
```

2. Second terminal - Run the tests:
```bash
cd examples/[example_name]  # same directory as above
./test.sh
```

A successful test run will show:
- All test cases executing without errors
- Expected JSON responses for successful operations
- Proper error handling for edge cases
- Server logs in the first terminal showing request handling

For example, a successful time skill test should show:
```
--- Testing Time Skillet ---
1. Getting current time in UTC (default)...
{"iso_8601":"2025-06-12T04:46:33+00:00", ...}

2. Getting current time in America/New_York...
{"iso_8601":"2025-06-12T00:46:33-04:00", ...}

3. Testing with an invalid timezone...
{"detail":"400: Invalid timezone: 'Mars/Olympus_Mons'..."}
```

See each example's specific README for detailed API usage instructions and expected responses.

## Tutorials: Using Skillet in Your Applications

The `tutorials/` directory contains example applications that demonstrate how to integrate Skillet skills into your own applications. These tutorials show real-world usage patterns and best practices for developers who want to use Skillet skills in their projects.

### Available Tutorials

- [openai_time_demo](tutorials/openai_time_demo/README.md) - Shows how to use OpenAI's GPT models with the Skillet time skill. This tutorial demonstrates:
  - Setting up OpenAI function calling with Skillet endpoints
  - Making HTTP requests to Skillet services
  - Handling responses and errors
  - Building an interactive CLI application

Each tutorial includes:
- Complete working code with comments
- Clear setup instructions
- Dependencies and environment configuration
- Best practices for production use

### Using the Tutorials

1. Choose a tutorial that matches your use case
2. Follow the README instructions in the tutorial directory
3. Use the code as a template for your own application

The tutorials are designed to be minimal yet production-ready examples that you can build upon. They demonstrate how to:
- Make API calls to Skillet skills
- Handle authentication and environment variables
- Process responses and handle errors
- Structure your application code

For example, to try the OpenAI + Skillet time demo:
```bash
# First, start the Skillet time service
cd examples/anthropic_time
pip install -r requirements.txt
uvicorn skillet_runtime:app --reload

# In a new terminal, run the OpenAI demo
cd tutorials/openai_time_demo
pip install -r requirements.txt
python main.py
```
