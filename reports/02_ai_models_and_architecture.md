# AI Model & Architecture Analysis

This report details the AI infrastructure and specific models utilized within the WorldForge repository. The architecture operates on a two-step pipeline: **Warping** (3D vision geometry generation) and **Video Inference** (Video Diffusion generation).

### 1. 3D Vision / Warping Models
These models are utilized in the initial stage to calculate geometry, depth, and optical flow, outputting warped reference frames and masks along a target trajectory.
*   **VGGT (Visual Geometry Grounded Transformer):** An open-source model used for 3D scene warping from single or sparse images. It predicts camera poses and generates novel views.
*   **DepthCrafter:** An open-source model developed by Tencent, used for 4D dynamic warping from video sequences. It provides video sequence depth estimation to guide dynamic trajectory warping.
*   *Future Integration Mentions:* The documentation encourages exploring other vision models like DA3 (Depth-Anything-3), Pi3, Mega-SAM, and UniDepth, indicating the architecture is intended to be modular and model-agnostic at the warping stage.

### 2. Video Diffusion Models (VDMs)
These are the core generative models that take the warped frames and text prompts to synthesize the final photorealistic output.
*   **Wan-2.1 (Wan2.1-I2V-14B-480P / Wan2.1-I2V-14B-720P):** An open-source, large-scale video generation model. It is used for high-fidelity, high-quality video generation. The repository directly integrates its inference pipeline.
*   **LongCat-Video:** An open-source model optimized for efficient, long video generation. The repository supports both its standard and *distilled* checkpoints, providing an option for faster inference speeds compared to Wan-2.1.
*   *Future Integration Mentions:* The documentation suggests porting the framework to models like LTX-2.

### 3. Open-Source vs. Proprietary Breakdown
*   **Open-Source Models:** The entire workflow relies exclusively on open-source foundation models. VGGT, DepthCrafter, Wan-2.1, and LongCat-Video are all open-weights models available to the community.
*   **Proprietary Models:** There are no proprietary generative models (e.g., Sora, Runway Gen-3) utilized in the codebase.
*   **Re-trained Models & LoRAs:** *Zero.* The core architectural philosophy of WorldForge is a **training-free, zero-shot** inference paradigm. The system does not use LoRAs, fine-tuning, or specific retrained checkpoints to achieve spatial control. It dynamically intercepts and manipulates the latent space of the base models during the standard denoising process (via IRR, FLF, and DSG).

### Critic Mode Assessment
The architecture is highly modular and relies entirely on cutting-edge open-source computer vision models. However, the system relies on significantly heavy models (e.g., Wan-2.1 14B), meaning the hardware requirements for local inference are exceptionally steep, demanding high VRAM. The absence of fine-tuning is a massive advantage for generalizability, though it relies heavily on the underlying VDM's existing world priors being robust enough to handle the induced structural guidance.
