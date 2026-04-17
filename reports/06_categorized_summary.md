# Categorized Knowledge Base Summary

Based on the prompt's request to analyze the repository as a "knowledge base containing agentic coding best practices, skills, and schemas," it is crucial to reiterate that the repository is primarily a computer vision research codebase. However, analyzing the code structures reveals knowledge areas relevant to AI engineers building complex pipelines.

### 1. Complex Pipeline Orchestration (Bash/Python Integration)
*   **Skill:** Managing multi-stage machine learning inference pipelines.
*   **Knowledge Area:** The repository relies heavily on bash scripts (`run_test_case.sh`) to glue together disparate Python modules (e.g., passing output directories from `DepthCrafter` into `wan_for_worldforge`). This demonstrates how to decouple heavy AI models into modular execution blocks to manage memory.

### 2. Latent Space Manipulation (Advanced VDM Intervention)
*   **Skill:** Inference-time modification of diffusion processes.
*   **Knowledge Area:** The core logic in the repository (IRR, FLF, DSG) resides in modifying the sampling loops (`scheduling_*.py` and `pipeline_*.py`). It provides a masterclass in how to intercept latent variables during the denoising steps, apply mathematical guidance (like blending optical flow masks), and inject external structural data without altering model weights.

### 3. Cross-Model Dependency Management
*   **Skill:** Handling conflicting CUDA/Torch dependencies.
*   **Knowledge Area:** The `README.md` and `environment.yml` outline specific version locking for PyTorch (2.6.0), CUDA (12.4), and specialized acceleration libraries like `flash-attn`, `xformers`, and `pytorch3d`. It demonstrates the necessity of exact build strings (e.g., `cxx11abiFALSE`) when dealing with complex graphic operations.

### 4. Prompt Design for Video Generation
*   **Skill:** Crafting textual prompts for specific temporal behaviors.
*   **Knowledge Area:** The `prompts.py` file and documentation provide guidance on prompting VDMs. Specifically, to achieve a static scene with camera movement, the prompt must explicitly constrain motion (e.g., "completely frozen", "utterly motionless"). This highlights that structural guidance alone is insufficient; the language prior must align with the geometric intent.

### Critic Mode Assessment
The repository contains zero specific knowledge regarding Agentic Tool Calling, System Optimization for LLM routing, CI/CD for agents, or Agentic Schemas (JSON/YAML tools). It is a domain-specific codebase for Video Diffusion. Any autonomous agent interacting with this repo must learn to execute its bash scripts, not inherit agentic philosophies from it.
