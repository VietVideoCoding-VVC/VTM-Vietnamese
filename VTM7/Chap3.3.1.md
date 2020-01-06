## 3.1    Intra prediction

### 3.3.1    Intra mode coding with 67 intra prediction modes

To capture the arbitrary edge directions presented in natural video, the number of directional intra modes in VVC is extended from 33, as used in HEVC, to 65. The new directional modes not in HEVC are depicted as red dotted arrows in Figure 13, and the planar and DC modes remain the same. These denser directional intra prediction modes apply for all block sizes and for both luma and chroma intra predictions. 

In VVC, several conventional angular intra prediction modes are adaptively replaced with wide-angle intra prediction modes for the non-square blocks. Wide angle intra prediction is described in 3.3.1.2.

In HEVC, every intra-coded block has a square shape and the length of each of its side is a power of 2. Thus, no division operations are required to generate an intra-predictor using DC mode. In VVC, blocks can have a rectangular shape that necessitates the use of a division operation per block in the general case. To avoid division operations for DC prediction, only the longer side is used to compute the average for non-square blocks.

#### 3.3.1.1  Intra mode coding

​                                 ![Fig13](\imgs\Fig13.PNG)

Figure 13 – 67 intra prediction modes

To keep the complexity of the most probable mode (MPM) list generation low, an intra mode coding method with 6 MPMs is used by considering two available neighboring intra modes. The following three aspects are considered to construct the MPM list:

\-     Default intra modes 

\-     Neighbouring intra modes 

\-     Derived intra modes 

A unified 6-MPM list is used for intra blocks irrespective of whether MRL and ISP coding tools are applied or not. The MPM list is constructed based on intra modes of the left and above neighboring block. Suppose the mode of the left is denoted as *Left* and the mode of the above block is denoted as *Above*, the unified MPM list is constructed as follows: 

·     When a neighboring block is not available, its intra mode is set to Planar by default. 

·     If both modes *Left* and *Above* are non-angular modes: 

–     MPM list à {Planar, DC, V, H, V-4, V+4}

·     If one of modes *Left* and *Above* is angular mode, and the other is non-angular: 

–     Set a mode *Max* as the larger mode in *Left* and *Above*

–     MPM list as {Planar, *Max*, DC, *Max -1, Max +1*, *Max -2*}

·     If *Left* and *Above* are both angular and they are different:

–     Set a mode *Max* as the larger mode in *Left* and *Above*

–     if the difference of mode *Left* and *Above* is in the range of 2 to 62, inclusive

§ MPM list as {Planar, *Left*, *Above*, DC, *Max -1*, *Max +1*}

–     Otherwise

§ MPM list as {Planar, *Left*, *Above*, DC, *Max -2, Max +2*}

·     If *Left* and *Above* are both angular and they are the same:

–     MPM list à {Planar, *Left*, *Left -1, Left +1*, DC, *Left -2*}

Besides, the first bin of the mpm index codeword is CABAC context coded. In total three contexts are used, corresponding to whether the current intra block is MRL enabled, ISP enabled, or a normal intra block.

During 6 MPM list generation process, pruning is used to remove duplicated modes so that only unique modes can be included into the MPM list. For entropy coding of the 61 non-MPM modes, a Truncated Binary Code (TBC) is used. 

#### 1.1.1.2 Wide-angle intra prediction for non-square blocks

Conventional angular intra prediction directions are defined from 45 degrees to -135 degrees in clockwise direction. In VVC, several conventional angular intra prediction modes are adaptively replaced with wide-angle intra prediction modes for non-square blocks. The replaced modes are signalled using the original mode indexes, which are remapped to the indexes of wide angular modes after parsing. The total number of intra prediction modes is unchanged, i.e., 67, and the intra mode coding method is unchanged.

​    ![Fig14-A](\imgs\Fig14-A.png)



![Fig14-B](\imgs\Fig14-B.png)

Figure 14 – Reference samples for wide-angular intra prediction 

To support these prediction directions, the top reference with length 2W+1, and the left reference with length 2H+1, are defined as shown in Figure 14. 

The number of replaced modes in wide-angular direction mode depends on the aspect ratio of a block. The replaced intra prediction modes are illustrated in Table 3‑2

Table 3‑2 – Intra prediction modes replaced by wide-angular modes

| Aspect ratio    | Replaced intra prediction modes              |
| --------------- | -------------------------------------------- |
| $W / H == 16$   | Modes 12, 13, 14, 15                         |
| $W / H == 8$    | Modes 12, 13                                 |
| $W / H == 4$$   | Modes 2, 3, 4, 5, 6, 7, 8, 9, 10, 11         |
| $W / H == 2$    | Modes 2, 3, 4, 5, 6, 7                       |
| $W / H == 1$    | None                                         |
| $W / H == 1/2$  | Modes 61, 62, 63, 64, 65, 66                 |
| $W / H == 1/4$  | Modes 57, 58, 59, 60, 61, 62, 63, 64, 65, 66 |
| $W / H == 1/8$  | Modes 55, 56                                 |
| $W / H == 1/16$ | Modes 53, 54, 55, 56                         |

 

  ![Fig15](imgs\Fig15.PNG)

Figure 15 – Problem of discontinuity in case of directions beyond 45°

As shown in Figure 15, two vertically-adjacent predicted samples may use two non-adjacent reference samples in the case of wide-angle intra prediction. Hence, low-pass reference samples filter and side smoothing are applied to the wide-angle prediction to reduce the negative effect of the increased gap ∆pα. If a wide-angle mode represents a non-fractional offset. There are 8 modes in the wide-angle modes satisfy this condition, which are [-14, -12, -10, -6, 72, 76, 78, 80]. When a block is predicted by these modes, the samples in the reference buffer are directly copied without applying any interpolation. With this modification, the number of samples needed to be smoothing is reduced. Besides, it aligns the design of non-fractional modes in the conventional prediction modes and wide-angle modes.

In VVC, $4:2:2$ and $4:4:4$ chroma formats are supported as well as $4:2:0$. Chroma derived mode (DM) derivation table for $4:2:2$ chroma format was initially ported from HEVC extending the number of entries from 35 to 67 to align with the extension of intra prediction modes. Since HEVC specification does not support prediction angle below -135 degree and above 45 degree, luma intra prediction modes ranging from 2 to 5 are mapped to 2. Therefore chroma DM derivation table for $4:2:2$ chroma format is updated by replacing some values of the entries of the mapping table to convert prediction angle more precisely for chroma blocks. 

#### 3.3.1.3  Mode Dependent Intra Smoothing (MDIS)

Four-tap intra interpolation filters are utilized to improve the directional intra prediction accuracy. In HEVC, a two-tap linear interpolation filter has been used to generate the intra prediction block in the directional prediction modes (i.e., excluding Planar and DC predictors). In VVC, simplified 6-bit 4-tap Gaussian interpolation filter is used for only directional intra modes. Non-directional intra prediction process is unmodified. The selection of the 4-tap filters is performed according to the MDIS condition for directional intra prediction modes that provide non-fractional displacements, i.e. to all the directional modes excluding the following: 2, HOR_IDX, DIA_IDX, VER_IDX, 66. 

Depending on the intra prediction mode, the following reference samples processing is performed:

1. The directional intra-prediction mode is classified into one of the following groups:

   –     vertical or horizontal modes (HOR_IDX, VER_IDX),

   –     diagonal modes that represent angles which are multiple of 45 degree (2, DIA_IDX, VDIA_IDX),

   –     remaining directional modes;

2. If the directional intra-prediction mode is classified as belonging to group A, then then no filters are applied to reference samples to generate predicted samples;

3. Otherwise, if a mode falls into group B, then a [1, 2, 1] reference sample filter may be applied (depending on the MDIS condition) to reference samples to further copy these filtered values into an intra predictor according to the selected direction, but no interpolation filters are applied;

4. Otherwise, if a mode is classified as belonging to group C, then only an intra reference sample interpolation filter is applied to reference samples to generate a predicted sample that falls into a fractional or integer position between reference samples according to a selected direction (no reference sample filtering is performed).