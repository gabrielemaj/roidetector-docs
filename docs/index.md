# ROI Detector Documentation

Machine learning application for automated Region of Interest detection on fluorescence microscopy images of lab mice.

---

## What is ROI Detector?

ROI Detector is a web-based tool that uses deep learning to automatically identify and localize anatomical regions on mouse images. The application combines computer vision, interactive editing, and statistical analysis to streamline the ROI annotation workflow for researchers.

**Key capabilities:**

- **Automated prediction** of 7 anatomical ROIs per mouse

- **Dual-input models** leveraging both photograph and fluorescence data

- **Interactive editing** with drag-and-drop interface

- **Statistical analysis** including luminance profiling and significance testing

- **Export compatibility** with existing imaging system formats

---


## Features

### Automated ROI Prediction

Pre-trained ResNet50-based models predict 7 anatomical regions:

1. Head

2. Upper body

3. Mid body

4. Lower body

5. Genital area

6. Foot

7. Tail

### Dual-Input Support

Choose between:

- **Single-input model** - Photograph only

- **Dual-input model** - Photograph + fluorescence for enhanced accuracy

### Interactive Editing

- Drag-and-drop crop definition

- Move, rotate, and adjust predicted ROIs

- Keyboard shortcuts for precise control

- Constraint-based editing (ROIs stay within crop bounds)

### Fluorescence Analysis

- Luminance threshold visualization

- Intensity profile charting

- Paired t-test for signal significance

- User evaluation logging

### Export/Import

- Export to `AnalyzedClickInfo.txt` format (imaging system compatible)

- Import existing ROIs for editing

- Optional metadata file merging

---
