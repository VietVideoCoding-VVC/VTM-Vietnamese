# 3    Algorithm description of Versatile Video Coding

## 3.1    VVC coding architecture

As in most preceding standards, VVC has a block-based hybrid coding architecture, combining inter-picture and intra-picture prediction and transform coding with entropy coding. Figure 1 shows a general block diagram of the VTM7 encoder.

​                                 ![Fig01](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig01.png)

 Figure 1 – General block diagram of VTM7 encoder [To be updated]

The picture partitioning structure, which is further described in section 3.2, divides the input video into blocks called coding tree units (CTUs). A CTU is split using a quadtree with nested multi-type tree structure into coding units (CUs), with a leaf coding unit (CU) defining a region sharing the same prediction mode (e.g. intra or inter). In this document, the term ‘unit’ defines a region of an image covering all colour components; the term ‘block’ is used to define a region covering a particular colour component (e.g. luma), and may differ in spatial location when considering the chroma sampling format such as 4:2:0.

The other features of VTM7, including intra prediction processes, inter picture prediction processes, transform and quantization processes, entropy coding processes and in-loop filter processes, are covered in sections 3.3 to 3.7. As agreed in the 11th JVET meeting, the following features have been included in the VVC test model 3 on top of the bock tree structure.

- Intra prediction

  –     67 intra mode with wide angles mode extension

  –     Block size and mode dependent 4 tap interpolation filter

  –     Position dependent intra prediction combination (PDPC)

  –     Cross component linear model intra prediction

  –     Multi-reference line intra prediction

  –     Intra sub-partitions

  –     Weighted intra prediction with matrix multiplication

- Inter-picture prediction

  –     Block motion copy with spatial, temporal, history-based, and pairwise average merging candidates

  –     Affine motion inter prediction

  –     subblock based temporal motion vector prediction

  –     Adaptive motion vector resolution

  –     8x8 block based motion compression for temporal motion prediction

  –     High precision (1/16 pel) motion vector storage and motion compensation with 8-tap interpolation filter for luma component and 4-tap interpolation filter for chroma component

  –     Triangular partitions

  –     Combined intra and inter prediction

  –     Merge with MVD (MMVD)

  –     Symmetrical MVD coding

  –     Bi-directional optical flow

  –     Decoder side motion vector refinement

  –     Bi-prediction with CU-level weight

- Transform,     quantization and coefficients coding

  –     Multiple primary transform selection with DCT2, DST7 and DCT8

  –     Secondary transform for low frequency zone

  –     Subblock transform for inter predicted residual

  –     Dependent quantization with max QP increased from 51 to 63

  –     Transform coefficient coding with sign data hiding

  –     Transform skip residual coding

- Entropy Coding

  –     Arithmetic coding engine with adaptive double windows probability update

- In loop filter

  –     In-loop reshaping

  –     Deblocking filter with strong longer filter

  –     Sample adaptive offset

  –     Adaptive Loop Filter

- Screen content coding:

  –     Intra block copy with reference region restriction

  –     Palette coding mode

  –     Adaptive color transform

- 360-degree video     coding 

  –     Horizontal wrap-around motion compensation

- High-level syntax and parallel processing

  –     Reference picture management with direct reference picture list signaling

  –     Tile groups with rectangular shape tile groups