---
hide:
  - toc
---

# Developer Guide Overview

The ROI Detector is a full-stack machine learning application for predicting and analyzing Regions of Interest (ROIs) on fluorescence microscopy images of lab mice.

---

## Architecture Overview

The ROI Detector is built on a **three-tier architecture** that separates concerns between frontend, backend and machine learning inference.

### Frontend Layer

The browser-based interface handles all user interactions and visual feedback. Built with JavaScript and HTML, it manages:

- **Image display and manipulation** - Renders uploaded images with zoom and pan capabilities
- **Interactive crop definition** - Users draw and adjust bounding boxes around each mouse
- **ROI visualization** - Displays predicted regions with color-coded overlays
- **Real-time editing** - Supports drag-to-move, rotation, and keyboard-based adjustments
- **Statistical overlays** - Shows luminance thresholds and significance testing results

The frontend communicates with the backend exclusively through REST API calls, maintaining a stateless architecture.

### Backend Layer

A FastAPI server provides the computational backbone of the application:

- **Image preprocessing** - Converts TIFF files to 8-bit format, applies normalization, and handles rotation-invariant cropping
- **Model orchestration** - Loads and manages multiple trained models, routing requests based on user selection
- **Coordinate transformations** - Maps between canvas coordinates, image space, and normalized model inputs
- **Session management** - Maintains an in-memory cache of uploaded images using session IDs
- **Export formatting** - Generates output files compatible with the original imaging system

The backend is stateless except for the image cache, which is periodically cleared via housekeeping endpoints.

### Machine Learning Layer

Two ResNet50-based models provide ROI predictions:

- **Single-input model** - Trained on photograph data alone, suitable for basic ROI detection
- **Dual-input model** - Accepts both photograph and luminescence images, providing enhanced accuracy by leveraging complementary information

Both models output normalized coordinates (x, y, angle) for 7 anatomical ROIs per mouse. The backend denormalizes these predictions back to pixel coordinates before returning them to the frontend.

---

## Request-Response Flow

A typical user session follows this pattern:

1. **Image upload** - User drops a TIFF file onto the interface
2. **Preview generation** - Backend converts the image to PNG and returns a data URL with a session ID
3. **Crop definition** - User draws bounding boxes around each mouse in the image
4. **Prediction request** - Frontend sends crop coordinates to `/predict_rois` endpoint
5. **Model inference** - Backend extracts each crop, preprocesses it, and runs inference
6. **Coordinate transformation** - Predictions are mapped from model space back to original image coordinates
7. **Visualization** - Frontend renders ROIs as colored rectangles and circles on the canvas
8. **Export** - User clicks export, triggering client-side generation of `AnalyzedClickInfo.txt`

---

## Technology Stack

### Frontend
- **HTML5** - Application structure
- **JavaScript** - UI logic and interactions
- **Canvas API** - Image rendering and ROI visualization
- **Bootstrap 5** - UI components and styling

### Backend
- **FastAPI** - RESTful API framework
- **Python 3.11** - Core language
- **Pillow (PIL)** - Image processing
- **NumPy** - Numerical computations
- **TensorFlow/Keras** - Model inference

### Machine Learning
- **ResNet50** - Base architecture (transfer learning)
- **Custom Dense Layers** - ROI coordinate regression
- **TensorFlow 2.18** - Training and inference framework

---

## Key Features

### Interactive Editing
- Drag-and-drop crop creation and adjustment
- ROI selection, movement, and rotation
- Constraint-based movement (ROIs stay within parent crop)
- Keyboard shortcuts for precise control

### Statistical Analysis
- Luminance threshold visualization
- Paired t-test for signal significance (fluorescence vs. photo)
- ROI projection on luminance profile chart
- User evaluation logging with IP tracking

### Export/Import
- Export ROIs to `AnalyzedClickInfo.txt` format (compatible with imaging system)
- Import existing ROIs for editing
- Optional metadata file merging

---


## Next Steps

- [Installation Guide](installation.md) - Set up the development environment
- [Architecture Details](architecture.md) - Deep dive into system components
- [API Reference](api-reference.md) - Complete API documentation