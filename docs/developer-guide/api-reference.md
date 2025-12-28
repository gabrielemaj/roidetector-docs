---
hide:
  - toc
---

# API Reference

**Swagger docs**: `https://ai-roi-detector.biodata.di.unimi.it/api/docs`  
**Main technologies**: Python and FastAPI

**Run locally** - requires Python 3.11:
```bash
conda activate ottobrini
cd single_output
uvicorn api.server:app --port 4002 --reload --host 0.0.0.0
```

---

## API Overview

The ROI Detector API provides RESTful endpoints for image processing, model inference, and geometry utilities. All endpoints are prefixed with `/api` and documented automatically via FastAPI's Swagger UI.

**Core functionality:**

- **Image management** - Upload, preview, and cache TIFF/PNG/JPEG images

- **Model inference** - Run trained ResNet50 models on user-defined crops

- **Geometry utilities** - Calculate rotated rectangle corners and point-polygon distances

- **Data logging** - Save user evaluations to CSV for analysis

For complete endpoint documentation, parameters, and response schemas, visit the **Swagger docs** at `/api/docs`.

---

## Add a New Endpoint

Adding a new endpoint is as simple as adding a function to `api/server.py`. HTTP methods, path parameters, and query parameters are automatically inferred from the function signature. For more details, refer to [FastAPI's path operation documentation](https://fastapi.tiangolo.com/tutorial/path-params/).

Example:
```python
@api.get("/example")
def example_endpoint(param: str):
    return {"message": f"Hello {param}"}
```

---


## Work with TensorFlow Models

The API loads trained Keras models on-demand and caches them in memory:
```python
MODEL_CACHE = {}

def get_model(model_name: str):
    if model_name not in MODEL_CACHE:
        model_path = get_model_path(model_name)
        MODEL_CACHE[model_name] = tf.keras.models.load_model(
            model_path, compile=False
        )
    return MODEL_CACHE[model_name]
```

To add a new model:
1. Train and save to `outputs/run_YYYYMMDD_HHMMSS/fine_tune_last_n/model_finetuned_last_n.keras`
2. Add entry to `AVAILABLE_MODELS` dict in `api/server.py`
```python
AVAILABLE_MODELS = {
    "run_20251208_105627": {"dual_input": False},
    "run_20251219_171038": {"dual_input": True},
    "run_20250115_120000": {"dual_input": False}  # New model
}
```

---

## Work with Image Processing

The API uses Pillow (PIL) for image manipulation. Key functions:

### `auto_level_to_8bit(im, use_percentile=False)`

Normalizes 16-bit TIFF images to 8-bit range:
- `use_percentile=False` - Linear min-max scaling
- `use_percentile=True` - Percentile-based (1st-99.5th) for fluorescence images

### `make_model_canvas_and_meta(base_img, xc, yc, w, h, angle_rad, target_size)`

Extracts a rotated crop and prepares it for model inference:

1. Calculates rotated rectangle corners

2. Creates polygon mask

3. Centers crop on black canvas (maintains spatial context)

4. Resizes to model input size (224×224)

5. Applies ResNet50 preprocessing

Returns preprocessed array and metadata for reverse coordinate transformation.

---

## Security Considerations

### CORS Configuration

Only whitelisted origins can access the API:
```python
ALLOWED_ORIGINS = [
    "http://localhost:4001",
    "https://ai-roi-detector.biodata.di.unimi.it"
]
```

To add new origins, modify `ALLOWED_ORIGINS` in `api/server.py`.


---

## Next Steps

- [Architecture](architecture.md) - System design details
- [Installation](installation.md) - Setup guide
- [Overview](overview.md) - Project introduction