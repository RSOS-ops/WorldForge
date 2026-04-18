# 3D-to-AI Rendering Integration

The WorldForge framework is uniquely positioned to bridge traditional 3D graphics workflows with generative AI. Because the system relies fundamentally on a "Warping" phase followed by an "Inference" phase, it can integrate deeply with standard 3D rendering engines by bypassing the initial AI-warping step.

### Integration Strategies for 3D Engines (C4D, Unreal, Blender)

The WorldForge pipeline expects a specific set of assets entering the Video Diffusion Model (VDM) stage: **warped reference frames** and **motion/occlusion masks**. 3D software can generate these assets natively and with perfect mathematical accuracy, bypassing the need for DepthCrafter or VGGT.

1.  **Cinema 4D / Blender Integration:**
    *   **Workflow:** An artist animates a scene (or a simple proxy geometry) and a camera path.
    *   **Asset Generation:** The artist renders the beauty pass (or a base diffuse pass) for the first frame. For the subsequent frames, instead of rendering a full beauty pass, the engine renders UV/Motion Vector passes and Z-Depth. The engine can generate the exact warped sequence of the first frame projected onto the moving camera's perspective. More critically, the 3D engine can export mathematically perfect alpha masks representing disocclusions (areas hidden in frame 1 but revealed in frame X).
    *   **AI Handoff:** These precise frames and masks are fed into `infer_worldforge.py` alongside a prompt describing the intended realistic outcome. The VDM will "inpaint" the disocclusions and up-res the proxy geometry into a photorealistic result.

2.  **Unreal Engine (Real-Time Integration):**
    *   Unreal Engine can utilize render targets to output motion vectors and scene depth in real-time. A Python script via Unreal Engine's Python API could package these buffers and save them to disk (or pipe them) directly into the WorldForge inference scripts, enabling a workflow where a low-poly Unreal blockout is used to generate camera paths that are then materialized by Wan-2.1.

### Hardware Constraints (RTX 5090 / 5000 Class, 48GB-64GB RAM)

**The Critical Bottleneck:** Running 14-Billion parameter Image-to-Video models (like Wan-2.1 14B) locally is incredibly demanding.

*   **VRAM Utilization:** Wan-2.1 14B alone requires massive VRAM. To perform WorldForge inference, the system must hold the VDM, but it also implements **Dual-Path Self-Corrective Guidance (DSG)**. DSG requires calculating an unguided path alongside the guided path, effectively doubling the activation memory requirements during the `guide-steps`.
*   **RTX 5090 (32GB VRAM):** Running the full Wan-2.1 14B pipeline with DSG at 720p on a single RTX 5090 will likely encounter Out-of-Memory (OOM) errors unless deep optimizations (e.g., CPU offloading, sequential block execution, or severe quantization) are employed. The repository mentions "Multi-GPU parallel inference" as a TODO, indicating current single-GPU constraints.
*   **RTX 5000 Ada Generation (32GB VRAM) / 48GB-64GB configurations:** Multi-GPU setups (e.g., dual 3090s/4090s/5090s) providing 48GB+ combined VRAM are practically mandatory for the Wan-2.1 pipeline.
*   **The LongCat Alternative:** The repository explicitly provides `longcat_for_worldforge` as an alternative. LongCat supports distilled 16-step modes and is smaller. This is the *only* viable path for single-card 24GB-32GB setups. The provided Jupyter Notebook (`longcat_interactive.ipynb`) keeps weights in VRAM to avoid I/O overhead, which is a necessary optimization for iterative 3D-to-AI workflows on local workstations.

### Critic Mode Assessment
The framework acts as a powerful "AI render engine" for 3D paths, but its reliance on 14B parameter models creates a severe bottleneck for local workstations. The lack of built-in tensor-parallelism or DeepSpeed integration in the provided scripts means 3D artists with a single high-end GPU (even an RTX 5090) will struggle with OOM errors on the Wan-2.1 model, restricting them to the lower-quality LongCat distilled models.
