# SAM2 0923 Interactive & Composition Toolkit

This 0923 drop pairs an advanced SAM2-assisted annotator with a video compositor so you can go from raw footage to stylised overlays without touching external tooling. The two entry points live under `sam2/my_own`:

- `advanced_interactive_0923.py` - GPU-powered interactive segmentation with persistent prompts and batched mask export.
- `sam2_video_compose_0923.py` - mask-aware video compositor with relocation/original placement modes, blend styles, and brightness matching.

The sections below condense what each script does and how to drive them when publishing the project to GitHub.

---

## 1. `advanced_interactive_0923.py`

### Key capabilities
- **Frame bootstrap:** extracts JPG frames on demand (`extract_frame.py`) and reports total frame count + resolution.
- **Stateful prompting:** stores clicks/labels per frame/object inside `interactive_state.json` so you can stop, reload, or branch annotations.
- **Multi-object management:** each frame can hold multiple object IDs, with quick navigation (Left/Right arrows, `N` for new object, `R` to reset, `S`/`L` to save/load state).
- **Batched SAM2 inference:** streams batches of frames through `build_sam2_video_predictor`, carries forward last-frame logits for temporal stability, and gracefully reports per-batch latency.
- **Dual mask exports:** writes per-object masks as `mask_00012_obj_2.png` under `mask_dir/per_object` and frame-level unions under `mask_dir/combined`, ready for downstream rendering.
- **Render hook:** automatically calls `render_video.py` to visualize the combined masks on top of the original frames once processing finishes.

### CLI usage
```bash
python advanced_interactive_0923.py \
  --video traffic.mp4 \
  --frame-dir tmp_frames \
  --mask-dir output_masks \
  --output traffic_masks.mp4 \
  --checkpoint checkpoints/sam2.1_hiera_tiny.pt \
  --config configs/sam2.1/sam2.1_hiera_t.yaml \
  --fps 30 \
  --batch-size 80 \
  --state-file interactive_state.json
```

### Output layout
```
output_masks/
  per_object/mask_00000_obj_1.png
  per_object/mask_00000_obj_2.png
  combined/mask_00000.png
```

Bring your annotated clicks to a steady state, press `Enter`, and the script will iterate through all batches, emit timing stats, and render a preview MP4 (`--output`) for quick QA.

---

## 2. `sam2_video_compose_0923.py`

### Key capabilities
- **Mask indexing:** auto-detects whether the mask set is multi-object (`mask_00012_obj_2.png`) or single-object (`mask_00012.png`) and builds a frame/object index with fallbacks for missing frames.
- **Placement strategies:**  
  - `relocate` (default) crops the mask ROI, rescales it to a target `--size`, and repositions it using anchors (`--pos`) plus pixel offsets.  
  - `original` uses the original ROI but intersects background/object bounds to keep alignment even with mismatched resolutions. Multi-object masks automatically force this safer path and show an on-screen hint.
- **Blend modes & color harmony:** `normal`, `multiply`, `screen`, and `overlay` modes with optional brightness normalization (mean-Y with clamped gain and gamma) so the inserted object matches the background exposure.
- **Mask hygiene:** every mask is resized to the object video resolution, normalized, and cropped to tight bounding boxes; negligible masks are skipped.
- **Looping & preview:** `--loop` restarts both videos, `--preview` skips writing to disk, and `S` lets you snapshot individual composed frames.

### CLI usage
```bash
python sam2_video_compose_0923.py \
  --bg bg_video.mp4 \
  --obj traffic.mp4 \
  --mask output_masks/per_object \
  --out composed_result.mp4 \
  --placement relocate \
  --size 220 \
  --pos bottom-right \
  --offset-x -40 \
  --offset-y -30 \
  --blend-mode screen \
  --brightness-match \
  --loop
```

Hit `Q` to exit the preview window; the script logs frame counters, forced placement messages, and approximate FPS every ~30 frames.

---

## Suggested workflow for the README
1. **Annotate:** Run the interactive script to place positive/negative clicks per object, save state often, and press `Enter` to generate masks plus a verification render.
2. **Inspect masks:** Review the `per_object` PNGs (or the `combined` set if you just need silhouettes).
3. **Compose:** Feed the mask directory and your object/background videos into the composition script. Choose `relocate` when you want to shrink and reposition cutouts; keep `original` for shot-to-shot replacements or multi-object scenes.
4. **Publish:** Capture screenshots/GIFs of both the interactive UI and final composites, list the CLI options that matter to your use case, and push this README along with the scripts/masks to GitHub.

These notes are ready to drop into your repository so visitors immediately understand how the two 0923 tools cooperate end-to-end.
