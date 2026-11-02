# Brain CT Segmentation Using Adaptive Thresholding: Block Size and C

## Experiment 3 — Parameter Investigation

## 1. Objective

This experiment investigates how the **block size** and **C** parameters influence adaptive thresholding of a selected region of interest (ROI) in a brain CT image. The goal is to understand the parameters, compare their effects on binary masks, and assess the visual limitations of local thresholding. This is an image-processing exercise, not a validated tumor-detection method.

## 2. Image and ROI

- Input image: `Tumor1.jpg`, read as an 8-bit grayscale image (pixel intensities 0–255).
- Approximate ROI center: **(x = 125, y = 90)**.
- ROI dimensions: **35 × 35 pixels**.
- Initial adaptive thresholding settings: **blockSize = 11**, **C = 2**.
- Methods investigated: **Adaptive Mean** and **Adaptive Gaussian** thresholding.

The values are grayscale intensities in a JPG image, **not calibrated CT Hounsfield units (HU)**.

## 3. What Is Block Size?

**Block size** defines the width and height of the local neighborhood used to calculate a threshold for each pixel. For example, a block size of 11 uses an **11 × 11 neighborhood**, containing **121 pixels**. The neighborhood moves across the ROI as the algorithm classifies each pixel.

OpenCV requires an **odd integer greater than 1** for `blockSize` (for example, 3, 5, 11, 15, or 21).

A smaller block size responds more strongly to fine local intensity variations and noise. A larger block size averages over a wider area and may produce smoother local thresholds, but can reduce sensitivity to small details. The actual segmentation also depends on the image and C value.

**Proposed block-size experiment:** Test **5, 11, 15, and 21**, keeping **C = 2** and the thresholding method fixed. Compare the resulting masks using the same ROI.

## 4. What Is C?

**C** is a constant subtracted from the locally calculated mean or Gaussian-weighted mean. It shifts the threshold used to classify each pixel.

For a local mean of 130:

| C | Local threshold = 130 − C |
|---:|---:|
| −5 | 135 |
| 0 | 130 |
| 2 | 128 |
| 5 | 125 |
| 10 | 120 |

With **binary thresholding**, a pixel is white (255) when its intensity is **greater than** its local threshold; otherwise, it is black (0). Therefore, increasing C generally lowers the threshold and increases the number of white pixels. Decreasing C generally raises the threshold and reduces the number of white pixels.

**Proposed C experiment:** Test **−5, 0, 2, 5, and 10**, keeping **blockSize = 11** and the thresholding method fixed.

## 5. Mathematical Formulas

### Adaptive Mean Thresholding

For a pixel at position \((x,y)\), the local threshold is:

$$
T(x,y)=\frac{1}{N}\sum_{(i,j)\in W(x,y)} I(i,j)-C
$$

where:

- \(I(i,j)\) is the grayscale intensity of a neighboring pixel.
- \(W(x,y)\) is the local neighborhood centered at \((x,y)\).
- \(N\) is the number of pixels in that neighborhood (for an 11 × 11 block, \(N=121\)).
- \(C\) is the subtracted constant.

### Adaptive Gaussian Thresholding

$$
T(x,y)=\sum_{(i,j)\in W(x,y)} w(i,j)I(i,j)-C
$$

Here, \(w(i,j)\) are normalized Gaussian weights, giving nearby pixels different contributions to the local threshold.

### Binary Classification

$$
B(x,y)=
\begin{cases}
255, & I(x,y)>T(x,y)\\
0, & I(x,y)\leq T(x,y)
\end{cases}
$$

Thus, **block size determines which pixels contribute to the threshold**, while **C determines how much the threshold is shifted**.

## 6. Experimental Procedure

1. Load the grayscale brain CT JPG image.
2. Crop the same **35 × 35 ROI** used in the earlier thresholding experiments.
3. Apply adaptive mean and adaptive Gaussian thresholding with **blockSize = 11** and **C = 2**.
4. To study block size, vary only `blockSize` while keeping C and the method fixed.
5. To study C, vary only C while keeping block size and the method fixed.
6. Display and visually compare each binary mask with the original ROI and the previous Otsu result.
7. Record any fragmentation, isolated pixels, holes, and changes to the central bright region.

*The parameter sweeps above are proposed follow-up tests; they are not presented as results already obtained.*

## 7. Observations from the Initial Comparison

In the initial experiment using **blockSize = 11** and **C = 2**, both adaptive mean and adaptive Gaussian thresholding produced **fragmented black-and-white masks** in the selected ROI. The earlier **Otsu** result (reported threshold **124**) appeared to preserve a more coherent central bright region.

This suggests that **Otsu looked more suitable for this particular ROI under the tested settings**. It does **not** establish that Otsu is universally better or more accurate. Changing block size and C may change the adaptive results.

## 8. Limitations

- Local thresholding can be sensitive to noise and small intensity variations.
- The **35 × 35 ROI** is small, so neighborhood size can have a substantial effect.
- Different ROI positions or sizes may lead to different outcomes.
- Grayscale brightness alone cannot reliably distinguish tumor from surrounding anatomy.
- No expert-annotated reference mask was used; segmentation accuracy cannot be quantified from visual appearance alone.

## 9. Conclusion

Adaptive thresholding depends strongly on **block size** and **C**. Block size controls the neighborhood used to estimate the local threshold; C adjusts that threshold. The initial settings (**11**, **2**) are starting values, not proven optimal settings. A controlled parameter study—changing one parameter at a time—is needed to understand their effects on this ROI.

In the initial visual comparison, Otsu's thresholding appeared to yield a more coherent region than the two adaptive methods. Any claim of improved tumor segmentation accuracy would require an expert reference mask and quantitative evaluation.

