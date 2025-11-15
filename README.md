# SegAnyGaussians: 3D Object Reconstruction

This repository contains code for 3D reconstruction of objects using SegAnyGaussians framework with optimization pipeline.

## 📁 Project Structure

```
├── cv_hw_2.ipynb              # Main pipeline notebook
├── sneaker_cleaning.ipynb     # Post-processing utilities (optional)
└── data/                      # Input images/video (create this)
```

## 🚀 Quick Start

### Prerequisites

- Google Colab account (recommended: High RAM, GPU T4/L4/A100)
- Video or images of your object captured from multiple angles

### Main Pipeline (`cv_hw_2.ipynb`)

**Simply run all cells sequentially from top to bottom.**

The notebook contains the complete pipeline:

1. **Environment Setup** - Install dependencies and compile CUDA modules
2. **Data Upload** - Upload your video/images
3. **COLMAP Reconstruction** - Camera pose estimation and sparse reconstruction
4. **SAM Pre-processing** ⭐ - Background removal (currently active)
5. **3DGS Training** - Main model training
6. **Feature Extraction** - CLIP and SAM features (optional)
7. **Model Export** - Download final model

### Current Configuration

The notebook is configured with the **optimized SAM pre-processing pipeline**, which provides the best quality-to-performance ratio:

```python
# Active settings:
- SAM background masking: ✅ ENABLED
- MASK_INDEX: 1 (adjust based on your object)
- White background: ✅ ENABLED
- Post-processing: ❌ DISABLED (not needed)
```

**Expected runtime:** ~2 hours on L4 GPU

**Expected results:**
- Model size: ~280MB (70k Gaussians)
- Rendering: 90 FPS @ 1080p
- Clean object without background

### 📝 Important Notes

1. **MASK_INDEX Selection**: After SAM generates masks, you'll see a 2x5 grid visualization. The notebook is set to `MASK_INDEX = 1`, but **adjust this if your object appears under a different index**.

2. **Alternative Pipelines**: The notebook contains commented-out code for:
   - Baseline (no pre-processing)
   - Post-processing with DBSCAN
   
   These are kept for reference but the current SAM-only approach is recommended.

3. **Memory Management**: The notebook includes automatic memory cleanup between stages. If you encounter OOM errors:
   - Reduce `--num_sampled_rays` from 500 to 300
   - Increase `--resolution` from 2 to 4

## 🧹 Optional Post-Processing (`sneaker_cleaning.ipynb`)

**Use this only if you want to experiment with post-processing methods.**

This notebook provides point cloud cleanup utilities:

### When to use:
- You have an existing `.ply` point cloud file
- You want to remove artifacts or outliers
- You want to compare filtering methods

### Usage:

1. Upload your `scene_point_cloud.ply` file to Colab
2. Run the first cell to install `plyfile`
3. Run the second cell - it will:
   - Filter by opacity and distance
   - Apply DBSCAN clustering
   - Download cleaned `point_cloud_clean.ply`

**Note:** With the SAM pre-processing pipeline, this step is typically unnecessary.

## 📊 Expected Outputs

After running the main pipeline, you'll get:

```
model_for_gui.zip containing:
├── point_cloud/
│   └── iteration_30000/
│       └── scene_point_cloud.ply    # Main 3D model
├── cameras.json                      # Camera parameters
├── input.ply                         # Initial sparse cloud
└── data_path/                        # Sample images + features
    ├── images/
    ├── sparse/
    ├── clip_features/
    └── sam_masks/
```

## 🎯 Tips for Best Results

1. **Input Video Quality:**
   - Capture 360° around the object
   - Steady camera movement
   - Good lighting without harsh shadows
   - 30-60 seconds of footage

2. **Object Placement:**
   - Center the object in frame
   - Use a contrasting background
   - Avoid reflective surfaces in background

3. **COLMAP Success:**
   - Ensure enough texture on object
   - Maintain overlap between frames
   - Avoid motion blur

4. **SAM Mask Selection:**
   - Visually inspect the 10 mask options
   - Choose the one that best isolates your object
   - Update `MASK_INDEX` in the notebook if needed

## 🐛 Troubleshooting

**"COLMAP camera model OPENCV not handled"**
→ The notebook handles this automatically with `colmap image_undistorter`

**"CUDA out of memory"**
→ Reduce `--num_sampled_rays` or skip contrastive feature training

**"Wrong object selected by SAM"**
→ Change `MASK_INDEX` value after viewing the mask grid

**"No sparse reconstruction"**
→ Your video may lack sufficient texture/overlap - try re-capturing

## 📚 References

- [3D Gaussian Splatting](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)
- [SegAnyGAussians](https://jumpat.github.io/SAGA/)
- [Segment Anything Model (SAM)](https://segment-anything.com/)

## 📄 License

This project uses code from SegAnyGaussians and 3D Gaussian Splatting, which are licensed for research and non-commercial use.

---

**Ready to start? Open `cv_hw_2.ipynb` in Google Colab and run all cells! 🚀**

