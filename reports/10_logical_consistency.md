# Logical Consistency & Entropy

This audit assesses the repository for conflicting advice, architectural friction, and deprecated practices.

### 1. The Disconnect Between Warping and Inference
*   **Logical Friction:** The pipeline is conceptually unified ("WorldForge") but practically bifurcated. The user is instructed to run a 3D vision model (VGGT or DepthCrafter) and *then* manually pass the output directory to the inference model (Wan-2.1).
*   **Entropy in File Management:** This creates massive file system entropy. Generating a 50-frame video creates 50 high-res warped `.png` files and 50 high-res mask `.png` files. If a user wants to test 10 different camera paths, they must manage thousands of temporary files. A logically consistent AI pipeline should hold these intermediate tensors in VRAM or RAM and pass them directly to the inference model, deleting them upon completion.

### 2. Parameter Tuning Ambiguity
*   **Conflicting Advice:** The documentation states: *"We recommend tuning parameters for your specific scene... Start by adjusting `--guide-steps`... `--omega` controls DSG strength — 4 or 6 is recommended; too high causes artifacts."*
*   **The Problem:** The `run_test_case.sh` scripts provide a "parameter grid" that runs multiple combinations of these settings overnight. This indicates that the authors themselves do not have a mathematically robust way to determine the optimal parameters for a given scene. The framework relies on "guess and check" (brute-forcing parameter grids) rather than algorithmic certainty. This is highly entropic for an autonomous agent, which struggles to visually evaluate which output video in the grid is the "correct" one.

### 3. Outdated AI Practices
*   **No Automatic Prompt Generation:** The pipeline requires manual text prompts in `prompts.py`. The documentation advises: *"We recommend using an LLM (e.g., GPT, Gemini) to generate prompts."* A modern, cohesive AI framework should integrate an open-source vision-language model (VLM) like Qwen-VL or LLaVA to automatically analyze the input image and generate the required prompt, rather than forcing the user out of the pipeline to query ChatGPT.

### Critic Mode Assessment
The repository is logically consistent in its mathematical approach to latent space modification, but it suffers from high operational entropy. The pipeline is fragmented, relying on manual file shuffling and brute-force parameter sweeps. For an autonomous agent to use this, it would need to write a wrapper script that automatically evaluates the output quality of the parameter sweep, which is currently an unsolved computer vision problem.
