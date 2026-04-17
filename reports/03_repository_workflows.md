# Workflow & Pipeline Dynamics

The WorldForge repository orchestrates a strict sequential workflow, transforming static or dynamic visual inputs into trajectory-controlled video outputs. The pipeline fundamentally separates spatial understanding from generative synthesis.

### Stage 1: The Warping Pipeline (Geometry & Structure)
**Input Data:** Single images, sparse image sets, or short video sequences.
**Process:**
1.  **3D Understanding:** For static images, `vggt/run_warp.py` processes the input through the VGGT model to deduce the 3D geometry of the scene. For video, `DepthCrafter/warp_depthcrafter.py` processes the sequence to estimate depth across time.
2.  **Trajectory Definition:** User-provided parameters (e.g., `--direction`, `--degree`, `--look_at_depth`) define a virtual camera path.
3.  **Warping Execution:** The models warp the input pixels along the calculated 3D/4D geometry to match the target camera path.
**Asset Output:** A directory containing warped reference frames (`warp_*.png`) and corresponding binary motion/occlusion masks (`mask_*.png`).

### Stage 2: Prompting & Context Preparation
**Input Data:** The original input image/video and human intent.
**Process:**
1.  **Textual Grounding:** Users modify `prompts.py` to provide a text prompt describing the scene. The documentation explicitly advises LLM generation for prompt consistency. For static scenes, the prompt must enforce stillness (e.g., "utterly motionless") to prevent the VDM from hallucinating unwanted subject movement while the virtual camera moves.
**Asset Output:** Text prompts matched to test cases.

### Stage 3: Generative Inference Pipeline (Synthesis & Correction)
**Input Data:** Text prompts, the original image/first frame (as the visual condition), and the warped frames/masks from Stage 1.
**Process:**
1.  **Latent Initialization:** The base Image-to-Video model (Wan-2.1 or LongCat) encodes the visual condition into latent space.
2.  **Intra-Step Recursive Refinement (IRR):** During the diffusion denoising loop, the system loops over specific steps (`--guide-steps`), repetitively injecting the target trajectory geometry into the latent variables.
3.  **Flow-Gated Latent Fusion (FLF):** The system uses the masks from Stage 1 to isolate motion channels, ensuring that structural guidance is only applied where geometry is shifting, leaving the rest of the latent representation intact to avoid visual degradation.
4.  **Dual-Path Self-Corrective Guidance (DSG):** The system simultaneously runs an unguided diffusion path. It compares the trajectory-guided path against this unguided reference, applying a correction factor (`--omega`) to pull the output back toward the high-fidelity prior if the structural guidance causes divergence.
**Asset Output:** Final high-resolution, photorealistic `.mp4` video files conforming to the target camera path.

### Critic Mode Assessment
The pipeline is logically sound but highly disjointed at the user-experience level. It requires the manual passing of directories from the 3D vision step into the VDM step via bash scripts (`run_test_case.sh`). There is no unified Python orchestrator that handles end-to-end processing. The data moves purely through file system read/writes of `.png` sequences, which introduces I/O bottlenecks compared to passing tensors directly in VRAM between models.
