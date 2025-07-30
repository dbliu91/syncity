# GLB Export Enhancement for blend_gaussians.py

## Overview

The `blend_gaussians.py` script has been enhanced to export GLB (Graphics Language Binary) files in addition to MP4 videos. This allows you to generate 3D models that can be viewed in 3D viewers, imported into game engines, or used in other 3D applications.

## New Features

1. **Automatic GLB Export**: By default, the script now attempts to generate GLB files for both the vanilla (unblended) and blended scenes.

2. **Two GLB Outputs**:
   - `gaussians.glb`: The vanilla scene combining all original tiles
   - `gaussians_blended.glb`: The blended scene with stitched boundaries

3. **Mesh Data Handling**: The pipeline now decodes both Gaussian and mesh representations when GLB export is enabled.

## Usage

```bash
python blend_gaussians.py <grid_path> [options]
```

### New Parameter

- `--export_glb` (default: `True`): Enable/disable GLB file export

### Example

```bash
# With GLB export (default)
python blend_gaussians.py ./my_grid --compute_rescaled=True --stitch_slats=True

# Without GLB export
python blend_gaussians.py ./my_grid --compute_rescaled=True --stitch_slats=True --export_glb=False
```

## Output Files

When GLB export is enabled, the following files are generated:

1. **Point Cloud Files** (always generated):
   - `<grid_path>/gaussians.ply`: Vanilla scene Gaussian point cloud
   - `<grid_path>/gaussians_scene.ply`: Blended scene Gaussian point cloud

2. **GLB Files** (when mesh data is available):
   - `<grid_path>/gaussians.glb`: Vanilla scene 3D mesh with textures
   - `<grid_path>/gaussians_blended.glb`: Blended scene 3D mesh with textures

3. **Video Files** (always generated):
   - `<grid_path>/gaussians.mp4`: Vanilla scene rotation video
   - `<grid_path>/gaussians_blended.mp4`: Blended scene rotation video

## Technical Implementation

### Key Changes

1. **Modified Functions**:
   - `sample_slat()`: Now accepts `decode_mesh` parameter to decode mesh data
   - `sample_slat_cond()`: Similarly enhanced to decode mesh data
   - `get_rescaled_cropped_slat()`: Returns mesh data when available

2. **New Functions**:
   - `export_gaussian_to_glb()`: Exports Gaussian+mesh to GLB format
   - `combine_meshes()`: Combines multiple meshes with translations

3. **Mesh Collection**:
   - Vanilla meshes are collected during tile processing
   - Blended meshes are collected during stitching
   - Proper translations are applied to align meshes

### Dependencies

The GLB export functionality uses:
- `trimesh`: For mesh manipulation and GLB export
- `trellis.utils.postprocessing_utils`: For GLB generation
- Existing Trellis pipeline components for mesh decoding

## Limitations

1. **Memory Requirements**: Mesh generation requires additional GPU memory compared to Gaussian-only processing.

2. **Processing Time**: Decoding mesh data adds computational overhead.

3. **Mesh Availability**: GLB files are only generated when the pipeline successfully produces mesh data. If mesh generation fails, only PLY files will be created.

## Troubleshooting

If GLB files are not generated:

1. **Check Console Output**: Look for messages about mesh data availability
2. **GPU Memory**: Ensure sufficient GPU memory is available
3. **Pipeline Errors**: Check for any errors during mesh decoding

## Future Improvements

Potential enhancements could include:
- Mesh simplification options for smaller file sizes
- Custom texture resolution settings
- Separate mesh export for individual tiles
- Support for different mesh formats (OBJ, FBX, etc.)