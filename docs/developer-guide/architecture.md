---
hide:
  - toc
---

# Architecture

Deep dive into the ROI Detector's technical implementation.

---

## Frontend Architecture

### State Management

The application maintains global state through JavaScript variables:

- **Image data**: `photoImageData`, `fluoImageData`, natural dimensions
- **Crops**: Array of crop objects with position, size, rotation, and predicted ROIs
- **Selection**: `activeIndex` for crops, `selectedROI` for individual ROIs
- **View state**: Current view mode (`photo`/`fluo`), luminance threshold

### Canvas System

Two-layer rendering:

1. **Base layer** (`<img>`) - Displays the photograph or fluorescence image

2. **Overlay layer** (`<canvas>`) - Renders crops, ROIs, handles, and overlays

**Coordinate transformations** map between image space and canvas space:
```javascript
// Image → Canvas
imgToCanvas(x, y) = (x * scale + xOff, y * scale + yOff)

// Canvas → Image
canvasToImgPoint(x, y) = ((x - xOff) / scale, (y - yOff) / scale)
```

!!! note "Coordinate System"
    All coordinates are stored in **image space** (original pixel coordinates). Canvas transformations are applied only during rendering to account for viewport size and zoom level.

### Interaction System

Drag-and-drop system with modes:

- **Move** - Drag crop border or ROI body

- **Resize** - Drag corner/edge handles

- **Rotate** - Drag rotation handle, Shift+Scroll, or double-click ROI + drag

Keyboard shortcuts: Arrow keys move selected ROI by 5px increments with acceleration.

---

## Backend Architecture

### API Structure

FastAPI application in `api/server.py`:

- **Prefix**: `/api`

- **CORS**: Enabled for whitelisted origins

- **Documentation**: Auto-generated at `/api/docs`

### Image Cache

In-memory dictionary keyed by session ID:
```python
IMAGE_CACHE[f"original_{session_id}"] = PIL.Image  # Original TIFF
IMAGE_CACHE[f"preview_{session_id}"] = PIL.Image   # 8-bit preview
```

Cache is cleared via `/housekeeping` endpoint.

!!! warning "Memory Limitations"
    Images are stored in RAM. For production with many concurrent users, consider using other solutions instead of in-memory caching.

### Processing Pipeline

#### TIFF Normalization

16-bit TIFF → 8-bit for display:

- Standard images: Linear min-max scaling

- Fluorescence: Percentile-based (1st-99.5th) for better contrast


#### Crop Extraction

`make_model_canvas_and_meta()` function:

1. Calculate rotated corners with rotation matrix

2. Create polygon mask for crop region

3. Center crop on black canvas (maintains spatial context)

4. Resize to 224×224

5. Apply ResNet50 preprocessing

Returns preprocessed array + metadata for reverse coordinate transformation.

### Model Management

Models loaded on-demand and cached:
```python
MODEL_CACHE = {}  # {model_name: keras.Model}

AVAILABLE_MODELS = {
    "run_20251208_105627": {"dual_input": False},
    "run_20251219_171038": {"dual_input": True}
}
```

!!! info "Model Loading"
    Models are loaded **once** on first request and remain in memory for the lifetime of the server. Restart the server to reload updated models.

### Prediction Workflow

`/predict_rois` endpoint:

1. Load images from cache

2. Process each crop (extract, center, resize, preprocess)

3. Prepare batch input (single or dual)

4. Run inference: `model(x, training=False)`

5. Parse output (centers + angles)

6. Transform coordinates back to original image space

7. Return JSON with predictions

**Coordinate transformation:**
```
Normalized [0,1] → Canvas coords → Adjust for offset → Absolute image coords
```

---

## Machine Learning

### Model Architecture

Both models use:
```
Input(s) → ResNet50 (frozen) → GlobalAvgPool → Dense → Dropout → Output Heads
```

**Single-input model**: Photo only (224×224×3)  

**Dual-input model**: Photo + Fluorescence (concatenated after ResNet50)

**Output heads:**
- Centers: Dense(14, sigmoid) → (x, y) for 7 ROIs
- Angles: Dense(7, tanh) → rotation angles

### Fixed ROI Dimensions

ROI sizes are **constant** (normalized to image dimensions):
```javascript
ROI_W = [0.0929, 0.1251, 0.1928, 0.1939, 0.1195, 0.0907, 0.0330]
ROI_H = [0.1373, 0.1461, 0.1417, 0.1395, 0.1195, 0.0441, 0.0863]
```

Models predict **only position (x, y) and orientation (angle)**.

!!! note "Design Choice"
    Fixed ROI sizes simplify the model and improve generalization. Mouse anatomy is sufficiently consistent that learning position and orientation is sufficient for accurate ROI placement.

### Training Pipeline

Managed by `run.py`:

1. **`--tune`** - KerasTuner searches learning rate, dropout, dense units
2. **`--train`** - Train dense layers (ResNet50 frozen)
3. **`--finetune_last_n`** - Unfreeze last N ResNet50 layers, train with low LR
4. **`--finetune_all`** - Unfreeze entire network (optional)

**Custom metrics**: IoU and Coverage computed using Shapely for polygon intersections.


---


## Extensibility

### Adding Models
1. Save to `outputs/run_YYYYMMDD_HHMMSS/`
2. Add to `AVAILABLE_MODELS` dict
3. Frontend auto-detects via `/models` endpoint

!!! warning "Model Naming Convention"
    Use the format `run_YYYYMMDD_HHMMSS` for consistency. The frontend expects models in `outputs/{model_name}/fine_tune_last_n/model_finetuned_last_n.keras`.

---

## Next Steps

- [API Reference](api-reference.md) - Endpoint documentation
- [Installation](installation.md) - Setup guide