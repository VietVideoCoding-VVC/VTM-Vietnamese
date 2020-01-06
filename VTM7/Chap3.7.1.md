## 1.1    In-loop filters

There are totally three in-loop filters in VVC. Besides deblocking filter and SAO (the two loop filters in HEVC), adaptive loop filter (ALF) are applied. The order of the filtering process in the VVC is the deblocking filter, SAO and ALF. The SAO in VVC is the same as that in HEVC.

In VVC, a new process called the luma mapping with chroma scaling was added (this process was previously known as the adaptive in-loop reshaper). This new process is performed before deblocking. 

### 1.1.1    Adaptive Loop Filter

In VVC, an Adaptive Loop Filter (ALF) with block-based filter adaption is applied. For the luma component, one among 25 filters is selected for each 4×4 block, based on the direction and activity of local gradients.

#### 1.1.1.1    Filter shape

Two diamond filter shapes (as shown in Figure 47) are used. The 7×7 diamond shape is applied for luma component and the 5×5 diamond shape is applied for chroma components.

​                               

Figure 47 – ALF filter shapes (chroma: 5×5 diamond, luma: 7×7 diamond)

#### 1.1.1.2    Block classification 

For luma component, each   block is categorized into one out of 25 classes. The classification index *C* is derived based on its directionality   and a quantized value of activity   , as follows:

​                                   (3-49)

To calculate   and   , gradients of the horizontal, vertical and two diagonal direction are first calculated using 1-D Laplacian:

​         (3-50)

​       (3-51)

 

​     (3-52)

​     (3-53)

Where indices   and   refer to the coordinates of the upper left sample within the   block and   indicates a reconstructed sample at coordinate   .

To reduce the complexity of block classification, the subsampled 1-D Laplacian calculation is applied. As shown in Figure 48, the same subsampled positions are used for gradient calculation of all directions.

|                                                 |                                                   |
| ----------------------------------------------- | ------------------------------------------------- |
| (a)  Subsampled positions for vertical gradient | (b)  Subsampled positions for horizontal gradient |
|                                                 |                                                   |
| (c)  Subsampled positions for diagonal gradient | (d)  Subsampled positions for diagonal gradient   |

**Figure** **48** **–** **Subsampled Laplacian calculation**

Then   maximum and minimum values of the gradients of horizontal and vertical directions are set as:

  ,                       (3-54)

The maximum and minimum values of the gradient of two diagonal directions are set as:

  ,                         (3-55)

To derive the value of the directionality   , these values are compared against each other and with two thresholds   and   :

**Step 1**.  If both   and   are true,   is set to   .

**Step 2**.  If   , continue from Step 3; otherwise continue from Step 4.

**Step 3**.  If   ,   is set to   ; otherwise   is set to   .

**Step 4**.  If   ,   is set to   ; otherwise   is set to   .

The activity value   is calculated as:

​                          (3-56)

  is further quantized to the range of 0 to 4, inclusively, and the quantized value is denoted as   .

For chroma components in a picture, no classification method is applied, i.e. a single set of ALF coefficients is applied for each chroma component.

#### 1.1.1.3    Geometric transformations of filter coefficients and clipping values

Before filtering each 4×4 luma block, geometric transformations such as rotation or diagonal and vertical flipping are applied to the filter coefficients   and to the corresponding filter clipping values   depending on gradient values calculated for that block. This is equivalent to applying these transformations to the samples in the filter support region. The idea is to make different blocks to which ALF is applied more similar by aligning their directionality.

Three geometric transformations, including diagonal, vertical flip and rotation are introduced:

Diagonal:                              (3-57)

Vertical flip:   ,             (3-58)

Rotation:   ,         (3-59)

where   is the size of the filter and   are coefficients coordinates, such that location   is at the upper left corner and location   is at the lower right corner. The transformations are applied to the filter coefficients *f* (*k*, *l*) and to the clipping values   depending on gradient values calculated for that block. The relationship between the transformation and the four gradients of the four directions are summarized in the following table.

**Table** **3****‑****12** **-** **Mapping of the gradient calculated for one block and the transformations**

| Gradient values        | Transformation    |
| ---------------------- | ----------------- |
| gd2 < gd1  and gh < gv | No transformation |
| gd2 < gd1  and gv < gh | Diagonal          |
| gd1 < gd2  and gh < gv | Vertical flip     |
| gd1 < gd2 and gv <  gh | Rotation          |

#### 1.1.1.4    Filter parameters signalling

ALF filter parameters are signalled in Adaptation Parameter Set (APS). In one APS, up to 25 sets of luma filter coefficients and clipping value indexes, and up to eight sets of chroma filter coefficients and clipping value indexes could be signalled. To reduce bits overhead, filter coefficients of different classification for luma component can be merged. In slice header, the indices of the APSs used for the current slice are signaled.

Clipping value indexes, which are decoded from the APS, allow determining clipping values using a table of clipping values for both luma and Chroma components. These clipping values are dependent of the internal bitdepth. More precisely, the clipping values are obtained by the following formula:

AlfClip                    (3-60)

with B equal to the internal bitdepth, a is a pre-defined constant value equal to 2.35, and N equal to 4 which is the number of allowed clipping values in VVC.

In slice header, up to 7 APS indices can be signaled to specify the luma filter sets that are used for the current slice. The filtering process can be further controlled at CTB level. A flag is always signalled to indicate whether ALF is applied to a luma CTB. A luma CTB can choose a filter set among 16 fixed filter sets and the filter sets from APSs. A filter set index is signaled for a luma CTB to indicate which filter set is applied. The 16 fixed filter sets are pre-defined and hard-coded in both the encoder and the decoder.

For chroma component, an APS index is signaled in slice header to indicate the chroma filter sets being used for the current slice. At CTB level, a filter index is signaled for each chroma CTB if there is more than one chroma filter set in the APS.

The filter coefficients are quantized with norm equal to 128. In order to restrict the multiplication complexity, a bitstream conformance is applied so that the coefficient value of the non-central position shall be in the range of −27 to 27 − 1, inclusive. The central position coefficient is not signalled in the bitstream and is considered as equal to 128.

#### 1.1.1.5    Filtering process

At decoder side, when ALF is enabled for a CTB, each sample   within the CU is filtered, resulting in sample value   as shown below,

​       (3-61)

where   denotes the decoded filter coefficients,   is the clipping function and   denotes the decoded clipping parameters. The variable k and l varies between   and   where *L* denotes the filter length. The clipping function   which corresponds to the function  

#### 1.1.1.6    Virtual boundary filtering process for line buffer reduction

In VVC, to reduce the line buffer requirement of ALF, modified block classification and filtering are employed for the samples near horizontal CTU boundaries. For this purpose, a virtual boundary is defined as a line by shifting the horizontal CTU boundary with “N” samples as shown in Figure 49, with N equal to 4 for the Luma component and 2 for the Chroma component. 

 

**Figure** **49** **–** **Modified block classification at virtual boundaries**

Modified block classification is applied for the Luma component as depicted in Figure 49. For the 1D Laplacian gradient calculation of the 4x4 block above the virtual boundary, only the samples above the virtual boundary are used. Similarly for the 1D Laplacian gradient calculation of the 4x4 block below the virtual boundary, only the samples below the virtual boundary are used. The quantization of activity value A is accordingly scaled by taking into account the reduced number of samples used in 1D Laplacian gradient calculation.

For filtering processing, symmetric padding operation at the virtual boundaries are used for both Luma and Chroma components. As shown in Figure 50, when the sample being filtered is located below the virtual boundary, the neighboring samples that are located above the virtual boundary are padded. Meanwhile, the corresponding samples at the other sides are also padded, symmetrically.

 

 

**Figure** **50** **–** **Modified ALF filtering for Luma component at virtual boundaries**

Different to the symmetric padding method used at horizontal CTU boundaries, simple padding process is applied for slice, tile and subpicture boundaries when filter across the boundaries is disabled. The simple padding process is also applied at picture boundary. The padded samples are used for both classification and filering process.