# Virtual Production Setup

WorldForge's zero-shot camera control paradigm presents compelling use cases for virtual production, bridging physical set data with generative background synthesis.

### Integration with Greenscreen & High-Resolution Feeds
In a traditional virtual production setup, actors perform against a greenscreen while a camera films them.
*   **The WorldForge Pipeline:** The high-resolution camera feed is keyed (greenscreen removed) to isolate the actor. The WorldForge framework is used to generate the *background plate*.
*   **Methodology:** A single concept image of the desired background (e.g., a sci-fi landscape) is fed into the system. As the physical camera moves, the movement data is passed to WorldForge, which generates a background video that perfectly tracks the physical camera's perspective, without needing a full 3D environment modeled beforehand. The generated background is then composited behind the live-action plate.

### Handling Motion-Tracked Cameras
WorldForge relies on coordinate geometry (`--direction`, `--degree`, etc.) to define camera movement in its test scripts.
*   **Real-time Tracking Integration:** Physical cameras in virtual production use tracking systems (OptiTrack, Vicon, or Vive trackers) to output real-time XYZ and pitch/yaw/roll data. To use WorldForge, this tracking data must be translated into the relative transformation matrices expected by the warping modules (VGGT/DepthCrafter) or passed directly as rendering matrices.
*   **Latency:** WorldForge is *not* a real-time system. Generating a sequence through Wan-2.1 takes minutes to hours. Therefore, it cannot be used for live LED volume displays (In-Camera VFX) during shooting.

### Cinema 4D / Unreal Engine Camera Integration (Baked vs. Real-Time)
Because WorldForge is strictly offline/inference-based, its virtual production utility is locked to post-production or pre-visualization.
*   **Pre-Visualization (Pre-vis):** An operator in Unreal Engine blocking out a scene can export a baked camera path. A lightweight script can pipe this path into WorldForge using the LongCat distilled model to generate high-quality pre-vis backgrounds overnight, replacing traditional low-poly grayboxing with photorealistic generative video.
*   **Post-Production (Baked Tracking):** Once principal photography is complete, the baked camera tracking data (e.g., an `.fbx` camera exported from Nuke, SynthEyes, or Unreal) is used. The WorldForge repository does not currently include a parser for `.fbx` or `.alembic` camera data. A custom translation layer must be written to convert standard 3D camera matrices into the frame-by-frame warping instructions that the WorldForge pipeline requires.

### Critic Mode Assessment
While conceptually powerful for generating backplates, WorldForge currently lacks the specific tooling (e.g., FBX camera importers, matrix-to-warp translation scripts) required for out-of-the-box virtual production. It is fundamentally an offline batch processor. Its inability to run in real-time precludes it from live LED wall use, restricting it strictly to pre-vis generation or post-production background synthesis.
