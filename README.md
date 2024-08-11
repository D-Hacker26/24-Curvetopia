# Curvetopia: A Journey into the World of Curves 🚀

### Overview

Welcome to Curvetopia! This project aims to explore, identify, regularize, and beautify various types of 2D curves. We'll begin with basic closed curves and progressively work towards handling more complex shapes. Our journey will also delve into symmetry and curve completion techniques.

### Objective

The primary goal is to develop an end-to-end process that converts a PNG (raster) image of line art into a set of curves defined as connected sequences of cubic Bézier curves. Initially, we'll simplify the input by using polylines (sequences of points) instead of PNG images.

### Key Tasks:

1. **Regularize Curves**: Identify and regularize shapes such as straight lines, circles, ellipses, rectangles, rounded rectangles, regular polygons, and star shapes.
2. **Explore Symmetry in Curves**: Identify reflection symmetries in closed shapes and transform these into Bézier curves.
3. **Complete Incomplete Curves**: Develop algorithms to complete curves with gaps due to occlusion, ensuring natural and smooth completion.

## Input and Output

- **Input**: A set of polylines representing 2D curves.
- **Output**: A set of paths with properties such as regularization, symmetry, and completeness, represented in cubic Bézier form for SVG rendering.

## Installation and Setup

1. **Dependencies**:
    - Python 3.x
    - `numpy`
    - `matplotlib`
    - `svgwrite`
    - `cairosvg`
2. **Installation**:
    
    ```bash
    pip install numpy matplotlib svgwrite cairosvg
    ```
    

## Usage

### Reading and Visualizing Polylines

To read and visualize polylines from CSV files:

```python
pythonCopy code
import numpy as np
import matplotlib.pyplot as plt

def read_csv(csv_path):
    np_path_XYs = np.genfromtxt(csv_path, delimiter=',')
    path_XYs = []

    for i in np.unique(np_path_XYs[:, 0]):
        npXYs = np_path_XYs[np_path_XYs[:, 0] == i][:, 1:]
        XYs = []
        for j in np.unique(npXYs[:, 0]):
            XY = npXYs[npXYs[:, 0] == j][:, 1:]
            XYs.append(XY)
        path_XYs.append(XYs)
    return path_XYs

def plot(paths_XYs):
    fig, ax = plt.subplots(tight_layout=True, figsize=(8, 8))
    for i, XYs in enumerate(paths_XYs):
        c = 'b'
        for XY in XYs:
            ax.plot(XY[:, 0], XY[:, 1], c=c, linewidth=2)
    ax.set_aspect('equal')
    plt.show()
```

### Converting Polylines to SVG and PNG

To convert polylines to SVG and rasterize to PNG:

```python
pythonCopy code
import svgwrite
import cairosvg

def polylines2svg(paths_XYs, svg_path):
    W, H = 0, 0
    for path_XYs in paths_XYs:
        for XY in path_XYs:
            W, H = max(W, np.max(XY[:, 0])), max(H, np.max(XY[:, 1]))

    padding = 0.1
    W, H = int(W + padding * W), int(H + padding * H)

    dwg = svgwrite.Drawing(svg_path, profile='tiny', shape_rendering='crispEdges')
    group = dwg.g()
    for i, path in enumerate(paths_XYs):
        path_data = []
        c = 'black'
        for XY in path:
            path_data.append(("M", (XY[0, 0], XY[0, 1])))
            for j in range(1, len(XY)):
                path_data.append(("L", (XY[j, 0], XY[j, 1])))
            if not np.allclose(XY[0], XY[-1]):
                path_data.append(("Z", None))
        group.add(dwg.path(d=path_data, fill=c, stroke='none', stroke_width=2))
    dwg.add(group)
    dwg.save()

    png_path = svg_path.replace('.svg', '.png')
    fact = max(1, 1024 // min(H, W))
    cairosvg.svg2png(url=svg_path, write_to=png_path, parent_width=W, parent_height=H,
                     output_width=fact * W, output_height=fact * H, background_color='white')
```

## Expected Results

- **Regularization**: Output paths will exhibit regular geometric shapes like rectangles, circles, and polygons with symmetry.
- **Symmetry**: Symmetrical properties will be highlighted, and curves will be transformed to Bézier format.
- **Curve Completion**: Incomplete curves will be naturally completed, considering smoothness, regularity, and symmetry.

## Evaluation

Evaluation will be based on the number of regular shapes identified, symmetry detected, and the accuracy of curve completion. The output will be compared against expected results using rasterized SVG images.

## Contributing

Feel free to submit issues, feature requests, and pull requests to enhance the functionality of Curvetopia.

## **Contributors**

- Sarthak Verma: [GitHub Profile](https://github.com/sarthakvermaa)
- Deepanshu Gupta: [GitHub Profile](https://github.com/D-Hacker26)
- Lavish Meena: [GitHub Profile](https://github.com/lavishmeena2764)
