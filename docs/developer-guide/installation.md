---
hide:
  - toc
---

# Installation

This guide walks you through setting up the ROI Detector development environment.

---

## Prerequisites

- **Operating System**: Linux (tested on Ubuntu 24)
- **Python**: 3.11
- **CUDA**: 12.4 (for GPU acceleration)
- **Conda**: Miniconda or Anaconda


---

## Environment Setup

### Create Conda Environment

The project uses a Conda environment with all required dependencies:
```bash
conda env create -f environment.yml
conda activate ottobrini
```

This will install:

- **FastAPI & Uvicorn** - Backend server
- **TensorFlow 2.18** - ML framework with CUDA 12.4 support
- **Pillow, NumPy, Shapely** - Image and geometry processing
- **scikit-learn, scipy** - Statistical analysis
- **Jupyter, Matplotlib** - Development and visualization

---


## Running the Application

### Start the Backend Server
```bash
uvicorn api.server:app --port 4002 --reload --host 0.0.0.0
```

### Start the Frontend

For development, use a simple HTTP server:
```bash
# From Src/single_output directory
python -m http.server 4001
```

## Next Steps

- [Architecture Details](architecture.md) - Understand system components
- [API Reference](api-reference.md) - Explore available endpoints