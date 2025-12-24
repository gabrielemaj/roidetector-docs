---
hide:
    - toc
---

# **Work instructions**

This guide explains how to use the ROI Predictor application to predict and manage Regions of Interest on lab mice images.

---

## **Getting Started**

### Loading an Image

1. Click **Load Photo Image** to select a photograph (supports TIFF and common image formats)
2. The image will appear in the central canvas area
3. You can also drag and drop an image directly onto the canvas

!!! tip "Supported Formats"
    The application works best with `.tif` / `.tiff` files from the imaging system. Standard formats like PNG and JPEG are also supported.

---

### Selecting a Model

Use the **ML model** switch at the top of the sidebar to choose the prediction model:

| Mode | Description |
|------|-------------|
| **Off** (default) | Single-input model — uses only the photograph |
| **On** (Luminescence) | Dual-input model — requires both photograph and luminescence image |

!!! info "Dual-Input Model"
    When the dual-input model is selected, the **Load Luminescence** button becomes available. You must load the luminescence image before processing.

---

## **Working with Crops**

Crops define the regions containing individual mice that will be processed by the model.

### Adding a Crop

1. Click **Add Crop** to create a new bounding box
2. The crop appears centered on the image
3. Each crop is assigned a unique color for easy identification

### Adjusting a Crop

| Action | How to |
|--------|--------|
| **Move** | Click and drag the crop border |
| **Resize** | Drag the corner or edge handles |
| **Rotate** | Drag the circular handle above the crop, or hold ++shift++ and scroll |

!!! note "Multiple Crops"
    You can add multiple crops for images containing more than one mouse. Each crop is processed independently.

### Removing Crops

- Click the **×** button on a crop's top-right corner to remove it individually
- Click the **🗑️** button (bottom-right) to clear all crops at once

---

## **Predicting ROIs**

Once you have defined the crops:

1. Click **Process ROIs**
2. The model predicts **7 ROIs per crop**

### ROI Mapping

| ROI | Anatomical Region | Shape |
|-----|-------------------|-------|
| 1 | Head | Rectangle |
| 2 | Upper body | Rectangle |
| 3 | Mid body | Rectangle |
| 4 | Lower body | Rectangle |
| 5 | Genital area | Circle |
| 6 | Hind limbs | Rectangle |
| 7 | Tail | Rectangle |

!!! warning "Processing Requirements"
    For the dual-input model, ensure both photograph and luminescence images are loaded before clicking Process ROIs.

---

## **Editing ROIs**

### Selecting an ROI

Click on any ROI to select it. When selected:

- A **yellow dashed border** highlights the ROI
- The **control panel** appears below the canvas
- The ROI ID is displayed in the panel header

### Movement and Rotation

| Action | Method |
|--------|--------|
| **Move** | Single click + drag, or use control panel arrows, or ++arrow-up++ ++arrow-down++ ++arrow-left++ ++arrow-right++ keys |
| **Rotate** | Double click + drag (rectangles only) |

!!! note "Constraints"
    ROIs are constrained within their parent crop boundaries. Circular ROIs (ROI 5) cannot be rotated.

---

## **Luminescence View**

When working with the dual-input model, you can analyze fluorescence signals.

### Switching Views

Use the **image switch** below the canvas to toggle between:

- **Photo view** — standard photograph
- **Luminescence view** — fluorescence image with analysis tools

### Analysis Tools

In luminescence view, additional tools become available:

#### Threshold Slider

A vertical slider appears on the left side of the canvas:

- Adjust the luminance threshold (0-100%)
- Pixels **above** the threshold are highlighted within ROIs
- Highlighted pixels use the crop's color with transparency

#### Luminance Profile Chart

A chart appears below the canvas showing:

- Intensity profile along the crop's long axis
- Yellow dashed line indicating the current threshold
- Yellow shaded area showing the selected ROI projection

!!! tip "Interpreting the Chart"
    Peaks in the luminance profile indicate areas of high fluorescence signal. Use this to identify relevant signal regions.

---

### Control ROI

The Control ROI is a reference region used to check background light levels.

**To place a Control ROI:**

1. Double-click anywhere on the image
2. Confirm placement in the popup dialog
3. The Control ROI appears as a white circle

!!! info "Control ROI ID"
    The Control ROI is automatically assigned ID 8 for single-mouse images, or ID 15 for multi-mouse images.

---

### Evaluating Signal Relevance

In luminescence view, you can evaluate whether the fluorescence signal in an ROI is biologically relevant:

1. Right-click on an ROI that contains illuminated pixels
2. A popup asks: *"Is ROI X signal relevant?"*
3. Click **YES** or **NO** to record your evaluation

!!! note "Statistical Analysis"
    When an ROI is selected, the p-value from a paired t-test (fluorescence vs. photograph) is displayed next to the ROI.

---

## **Importing and Exporting**

### Loading Metadata

Optionally load an existing metadata file:

1. Click **Load Metadata (Optional)**
2. Select an `AnalyzedClickInfo.txt` file
3. The filename appears in the status indicator

!!! info "Metadata Merge"
    When exporting, ROIs will be merged into the loaded metadata file, preserving other information.

---

### Importing ROIs

Load previously saved ROI coordinates:

1. Click **Import ROIs**
2. Select a text file containing ROI data

??? info "Supported Import Formats"
    - Standard `AnalyzedClickInfo.txt` format with `ROI N: Xc=...; Yc=...;` syntax
    - Comma or space-separated coordinates
    - Key-value format: `xc=0.5 yc=0.3 angle=0`

---

### Exporting ROIs

Save predicted or edited ROIs:

1. After processing, click **Export ROIs**
2. A text file is downloaded automatically

The exported file contains:

- ROI coordinates scaled to original TIFF dimensions (1920px)
- Format compatible with the original imaging system
- All metadata from the loaded file (if any)

!!! warning "Overwrite Warning"
    If the loaded metadata already contains ROIs, you will be asked to confirm before overwriting.

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| ++arrow-up++ ++arrow-down++ ++arrow-left++ ++arrow-right++ | Move selected ROI |
| ++shift++ + Scroll | Rotate active crop |
| Double-click (on ROI) | Enter rotation mode |
| Double-click (on canvas) | Place Control ROI |
| Right-click (on ROI) | Evaluate signal (luminescence view) |

---

## Tips & Best Practices

!!! tip "Workflow Recommendations"
    1. Always load the **photograph first**, then the luminescence image
    2. The luminescence image is automatically resized to match photograph dimensions
    3. Adjust the **threshold slider** to filter background noise
    4. Use the **luminance chart** to identify signal peaks along the mouse body
    5. Place a **Control ROI** in a dark area to establish baseline levels
