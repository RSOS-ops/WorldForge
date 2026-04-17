# Security & Threat Examination

This audit examines the WorldForge repository for security risks, specifically focusing on the distinction between required operational capabilities and potential malicious vectors.

### 1. Agentic Capability vs. Malicious Intent
The repository is designed to execute heavy local builds, interact with the local file system to read/write large image sequences, and download multi-gigabyte model weights.
*   **Expected Agent Behavior:** An autonomous agent operating this repo will need to execute bash scripts (`run_test_case.sh`), create directories, move files, and invoke `pip install` or `conda install` commands as outlined in the README.
*   **Threat Assessment:** The bash scripts provided in the repository (e.g., `vggt/run_test_case.sh`) are entirely benign. They contain standard Python invocation commands with arguments. There are no hidden curl commands piping to bash (`curl | bash`), no obfuscated binary downloads, and no exfiltration vectors.

### 2. Dependency Sourcing & Build Instructions
*   **The Flash-Attention Risk:** The README provides specific instructions to install `flash-attn` via a direct pre-built wheel URL from a GitHub release (`https://github.com/Dao-AILab/flash-attention/releases/...`). While Dao-AILab is the legitimate maintainer, instructing an automated agent to blindly download and execute `.whl` files from URLs bypasses standard package manager security checks. An attacker who compromises that GitHub release could execute arbitrary code upon installation.
*   **Git Installations:** The README instructs users to install `pytorch3d` and `LightGlue` directly via git URLs (`git+https://github.com/...`). While standard practice in ML research, this carries a persistent risk of the target repository being compromised, leading to immediate supply-chain infection for anyone cloning the repo.

### 3. Prompt Injection Risks
*   **System Prompts:** The repository uses `prompts.py` to store text strings passed to the VDM. There are no LLM agents interpreting these prompts; they are converted to embeddings. Therefore, traditional "Prompt Injection" (tricking an LLM into disobeying instructions) is not applicable.
*   **Malicious Prompts:** If a user/agent passes malicious or illegal imagery descriptions into `prompts.py`, the VDM will attempt to generate them. The repository does not appear to contain built-in NSFW filters or safety checkers in the inference pipelines, relying entirely on the base models' (Wan-2.1) inherent safety tuning, which can often be bypassed.

### 4. Unsafe Execution & Sandboxing
*   The system executes arbitrary Python code and relies heavily on complex C++ CUDA extensions. Due to the high hardware requirements, running this inside a strictly isolated Docker sandbox is difficult, as it requires bare-metal GPU passthrough. A vulnerability in any of the massive dependency chains (PyTorch, xformers, etc.) could compromise the host machine.

### Critic Mode Assessment
The repository is a standard, benign academic ML project. However, the installation instructions rely on direct GitHub URLs for `.whl` and `git+` installations. If an autonomous agent parses the README and executes those commands without verifying checksums, it is highly vulnerable to supply-chain attacks. Agents should be configured to verify the hashes of the downloaded wheels before installation.
