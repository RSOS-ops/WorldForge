# Agent Readiness

This audit evaluates how easily another autonomous agent (e.g., Devin, Claude-Engineer, AutoGPT) could index, understand, and utilize this repository.

### 1. Current Agent Readiness: Poor
*   **No Machine-Readable Schemas:** As noted in previous reports, there are no OpenAPI specs or JSON tool schemas. An agent cannot simply `import` WorldForge and know what parameters to pass.
*   **Execution Abstraction:** The core logic is hidden behind `argparse` implementations inside bash scripts. An agent must read the Python source code of `infer_worldforge.py` to deduce what `--omega` means and its acceptable float ranges.
*   **No State Reporting:** The bash scripts do not return structured JSON upon completion. They output standard `stdout` logs and write `.mp4` files to a directory. An agent must parse the `stdout` text stream to determine if the job succeeded or hit an Out-of-Memory (OOM) error.

### 2. The Missing `AGENTS.md`
To make this repository "Agent Ready," it critically requires an `AGENTS.md` file located in the root directory.

#### Proposed `AGENTS.md` Structure:
An `AGENTS.md` file should be structured specifically to guide an LLM parsing the codebase:

1.  **System Requirements & Environment Bounds:**
    *   Explicitly state the VRAM requirements (e.g., "Do not attempt to run Wan-2.1 if the host machine has less than 40GB VRAM. Default to LongCat.").
    *   Explicitly state the OS requirements ("If on Windows, do not attempt to run bash scripts directly. Abort or request WSL2.").
2.  **Tool Execution Schemas:**
    *   Provide strict JSON schemas defining exactly how to invoke the Python scripts.
    *   Example: Define the bounds of `--guide-steps` (Integer, Min: 10, Max: 30) and `--omega` (Float, Min: 2.0, Max: 8.0).
3.  **Error Handling Protocols:**
    *   Instruct the agent on what to do if an OOM error occurs (e.g., "If `torch.cuda.OutOfMemoryError` is detected in stdout, reduce `--resolution` to 480p and attempt execution again").
4.  **Workflow Mapping:**
    *   Clearly map the expected input/output file paths. "Step 1: Execute VGGT. Output is at `/vggt/output/`. Step 2: Pass `/vggt/output/` as `--video-ref` to `infer_worldforge.py`."

### Critic Mode Assessment
In its current state, this repository is built by human researchers *for* human researchers. An autonomous agent will struggle to operate it reliably without hallucinating parameters, failing dependencies (especially on Windows), or crashing the host machine via VRAM exhaustion. Implementing a strict `AGENTS.md` with explicit tool schemas and error-handling fallback logic is mandatory before this can be considered a reliable node in an automated AI workflow.
