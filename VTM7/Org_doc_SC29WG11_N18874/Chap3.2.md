## 3.2    Partitioning

### 3.2.1    Partitioning of the picture into CTUs

Pictures are divided into a sequence of coding tree units (CTUs). The CTU concept is same to that of the HEVC [6][7]. For a picture that has three sample arrays, a CTU consists of an N×N block of luma samples together with two corresponding blocks of chroma samples.Figure 2 shows the example of a picture divided into CTUs.

The maximum allowed size of the luma block in a CTU is specified to be 128×128 (although the maximum size of the luma transform blocks is 64×64).

​                                ![Fig02](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig02.png)

Figure 2 – Example of a picture divided into CTUs

### 3.2.2    Partitioning of pictures into subpictures, slices, tiles

A picture is divided into one or more tile rows and one or more tile columns. A tile is a sequence of CTUs that covers a rectangular region of a picture.

A slice consists of an integer number of complete tiles or an integer number of consecutive complete CTU rows within a tile of a picture.

Two modes of slices are supported, namely the raster-scan slice mode and the rectangular slice mode. In the raster-scan slice mode, a slice contains a sequence of complete tiles in a tile raster scan of a picture. In the rectangular slice mode, a slice contains either a number of complete tiles that collectively form a rectangular region of the picture or a number of consecutive complete CTU rows of one tile that collectively form a rectangular region of the picture. Tiles within a rectangular slice are scanned in tile raster scan order within the rectangular region corresponding to that slice.

A subpicture contains one or more slices that collectively cover a rectangular region of a picture.

Figure 3 shows an example of raster-scan slice partitioning of a picture, where the picture is divided into 12 tiles and 3 raster-scan slices.

  ![Fig03](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig03.png)

Figure 3 – Example of a picture partitioned into tiles and reaster-scan slices

Figure 4 shows an example of rectangular slice partitioning of a picture, where the picture is divided into 24 tiles (6 tile columns and 4 tile rows) and 9 rectangular slices.

  ![Fig04](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig04.png)

Figure 4 – Example of a picture partitioned into tiles and rectangular slices

Figure 5 shows an example of a picture partitioned into tiles and rectangular slices, where the picture is divided into 4 tiles (2 tile columns and 2 tile rows) and 4 rectangular slices.

  ![Fig05](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig05.png)

Figure 5 – Example of a picture partitioned into 4 tiles and 4 rectangular slices

Figure 6 shows an example of subpicture partitioning of a picture, where a picture is partitioned into 28 subpictures of varying dimensions.

  ![Fig06](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig06.png)

Figure 6 – Example of a picture partitioned into 28 subpictures

### 3.2.3    Partitioning of the CTUs using a tree structure

In HEVC, a CTU is split into Cus by using a quaternary-tree structure denoted as coding tree to adapt to various local characteristics. The decision whether to code a picture area using inter-picture (temporal) or intra-picture (spatial) prediction is made at the leaf CU level. Each leaf CU can be further split into one, two or four Pus according to the PU splitting type. Inside one PU, the same prediction process is applied and the relevant information is transmitted to the decoder on a PU basis. After obtaining the residual block by applying the prediction process based on the PU splitting type, a leaf CU can be partitioned into transform units (Tus) according to another quaternary-tree structure similar to the coding tree for the CU. One of key feature of the HEVC structure is that it has the multiple partition conceptions including CU, PU, and TU.

In VVC, a quadtree with nested multi-type tree using binary and ternary splits segmentation structure replaces the concepts of multiple partition unit types, i.e. it removes the separation of the CU, PU and TU concepts except as needed for Cus that have a size too large for the maximum transform length, and supports more flexibility for CU partition shapes. In the coding tree structure, a CU can have either a square or rectangular shape. A coding tree unit (CTU) is first partitioned by a quaternary tree (a.k.a. quadtree) structure. Then the quaternary tree leaf nodes can be further partitioned by a multi-type tree structure. As shown in Figure 7, there are four splitting types in multi-type tree structure, vertical binary splitting (SPLIT_BT_VER), horizontal binary splitting (SPLIT_BT_HOR), vertical ternary splitting (SPLIT_TT_VER), and horizontal ternary splitting (SPLIT_TT_HOR). The multi-type tree leaf nodes are called coding units (Cus), and unless the CU is too large for the maximum transform length, this segmentation is used for prediction and transform processing without any further partitioning. This means that, in most cases, the CU, PU and TU have the same block size in the quadtree with nested multi-type tree coding block structure. The exception occurs when maximum supported transform length is smaller than the width or height of the colour component of the CU. 

 

  ![Fig07](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig07.png)

Figure 7 – Multi-type tree splitting modes

Figure 8 illustrates the signalling mechanism of the partition splitting information in quadtree with nested multi-type tree coding tree structure. A coding tree unit (CTU) is treated as the root of a quaternary tree and is first partitioned by a quaternary tree structure. Each quaternary tree leaf node (when sufficiently large to allow it) is then further partitioned by a multi-type tree structure. In the multi-type tree structure, a first flag (mtt_split_cu_flag) is signalled to indicate whether the node is further partitioned; when a node is further partitioned, a second flag (mtt_split_cu_vertical_flag) is signalled to indicate the splitting direction, and then a third flag (mtt_split_cu_binary_flag) is signalled to indicate whether the split is a binary split or a ternary split. Based on the values of mtt_split_cu_vertical_flag and mtt_split_cu_binary_flag, the multi-type tree slitting mode (MttSplitMode) of a CU is derived as shown in Table 3‑1.

  ![Fig08](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig08.png)

Figure 8 – Splitting flags signalling in quadtree with nested multi-type tree coding tree structure

**Table** **3****‑****1** **–** **MttSplitMode derviation based on multi-type tree syntax elements**

| **MttSplitMode** | **mtt_split_cu_vertical_flag** | **mtt_split_cu_binary_flag** |
| ---------------- | ------------------------------ | ---------------------------- |
| SPLIT_TT_HOR     | 0                              | 0                            |
| SPLIT_BT_HOR     | 0                              | 1                            |
| SPLIT_TT_VER     | 1                              | 0                            |
| SPLIT_BT_VER     | 1                              | 1                            |

 

Figure 9 shows a CTU divided into multiple Cus with a quadtree and nested multi-type tree coding block structure, where the bold block edges represent quadtree partitioning and the remaining edges represent multi-type tree partitioning. The quadtree with nested multi-type tree partition provides a content-adaptive coding tree structure comprised of Cus. The size of the CU may be as large as the CTU or as small as 4×4 in units of luma samples. For the case of the 4:2:0 chroma format, the maximum chroma CB size is 64×64 and the minimum chroma CB size is 2×2.

In VVC, the maximum supported luma transform size is 64×64 and the maximum supported chroma transform size is 32×32. When the width or height of the CB is larger the maximum transform width or height, the CB is automatically split in the horizontal and/or vertical direction to meet the transform size restriction in that direction.

  ![Fig09](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig09.png)

Figure 9– Example of quadtree with nested multi-type tree coding block structure

The following parameters are defined and specified by SPS syntax elements for the quadtree with nested multi-type tree coding tree scheme.

–  CTU size: the root node size of a quaternary tree

–  *MinQTSize*: the minimum allowed quaternary tree leaf node size

–  *MaxBtSize*: the maximum allowed binary tree root node size

–  *MaxTtSize*: the maximum allowed ternary tree root node size

–  *MaxMttDepth*: the maximum allowed hierarchy depth of multi-type tree splitting from a quadtree leaf

–  *MinBtSize*: the minimum allowed binary tree leaf node size

–  *MinTtSize*: the minimum allowed ternary tree leaf node size

In one example of the quadtree with nested multi-type tree coding tree structure, the CTU size is set as 128×128 luma samples with two corresponding 64×64 blocks of 4:2:0 chroma samples, the *MinQTSize* is set as 16×16, the *MaxBtSize* is set as 128×128 and *MaxTtSize* is set as 64×64, the *MinBtSize* and *MinTtSize* (for both width and height) is set as 4×4, and the *MaxMttDepth* is set as 4. The quaternary tree partitioning is applied to the CTU first to generate quaternary tree leaf nodes. The quaternary tree leaf nodes may have a size from 16×16 (i.e., the *MinQTSize*) to 128×128 (i.e., the CTU size). If the leaf QT node is 128×128, it will not be further split by the binary tree since the size exceeds the *MaxBtSize* and *MaxTtSize* (i.e., 64×64). Otherwise, the leaf qdtree node could be further partitioned by the multi-type tree. Therefore, the quaternary tree leaf node is also the root node for the multi-type tree and it has multi-type tree depth (mttDepth) as 0. When the multi-type tree depth reaches *MaxMttDepth* (i.e., 4), no further splitting is considered. When the multi-type tree node has width equal to *MinBtSize* and smaller or equal to 2 * *MinTtSize*, no further horizontal splitting is considered. Similarly, when the multi-type tree node has height equal to *MinBtSize and* smaller or equal to 2 * *MinTtSize*, no further vertical splitting is considered.

To allow 64×64 Luma block and 32×32 Chroma pipelining design in VVC hardware decoders, TT split is forbidden when either width or height of a luma coding block is larger than 64 , as shown in Figure 10. TT split is also forbidden when either width or height of a chroma coding block is larger than 32.

  ![Fig10](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig10.png)

**Figure** **10** **–** **No TT split for 128****×128 coding block**

In VTM7, the coding tree scheme supports the ability for the luma and chroma to have a separate block tree structure. Currently, for P and B slices, the luma and chroma CTBs in one CTU have to share the same coding tree structure. However, for I slices, the luma and chroma can have separate block tree structures. When separate block tree mode is applied, luma CTB is partitioned into Cus by one coding tree structure, and the chroma CTBs are partitioned into chroma Cus by another coding tree structure. This means that a CU in an I slice may consist of a coding block of the luma component or coding blocks of two chroma components, and a CU in a P or B slice always consists of coding blocks of all three colour components unless the video is monochrome.

### 3.2.4    CU splits on picture boundaries

As done in HEVC, when a portion of a tree node block exceeds the bottom or right picture boundary, the tree node block is forced to be split until the all samples of every coded CU are located inside the picture boundaries. The following splitting rules are applied in the VTM7:

–  If a portion of a tree node block exceeds both the bottom and the right picture boundaries,

–    If the block is a QT node and the size of the block is larger than the minimum QT size, the block is forced to be split with QT split mode.

–    Otherwise, the block is forced to be split with SPLIT_BT_HOR mode

–  Otherwise if a portion of a tree node block exceeds the bottom picture boundaries,

–    If the block is a QT node, and the size of the block is larger than the minimum QT size, and the size of the block is larger than the maximum BT size, the block is forced to be split with QT split mode.

–    Otherwise, if the block is a QT node, and the size of the block is larger than the minimum QT size and the size of the block is smaller than or equal to the maximum BT size, the block is forced to be split with QT split mode or SPLIT_BT_HOR mode.

–    Otherwise (the block is a BTT node or the size of the block is smaller than or equal to the minimum QT size), the block is forced to be split with SPLIT_BT_HOR mode.

–  Otherwise if a portion of a tree node block exceeds the right picture boundaries,

–    If the block is a QT node, and the size of the block is larger than the minimum QT size, and the size of the block is larger than the maximum BT size, the block is forced to be split with QT split mode.

–    Otherwise, if the block is a QT node, and the size of the block is larger than the minimum QT size and the size of the block is smaller than or equal to the maximum BT size, the block is forced to be split with QT split mode or SPLIT_BT_VER mode.

–    Otherwise (the block is a BTT node or the size of the block is smaller than or equal to the minimum QT size), the block is forced to be split with SPLIT_BT_VER mode.

### 3.2.5    Restrictions on redundant CU splits

The quadtree with nested multi-type tree coding block structure provides a highly flexible block partitioning structure. Due to the types of splits supported the multi-type tree, different splitting patterns could potentially result in the same coding block structure. In VVC, some of these redundant splitting patterns are disallowed.

Figure 11 illustrates the redundant splitting patterns of binary tree splits and ternary tree splits. As shown in Figure 11, two levels of consecutive binary splits in one direction could have the same coding block structure as a ternary tree split followed by a binary tree split of the central partition. In this case, the binary tree split (in the given direction) for the central partition of a ternary tree split is prevented by the syntax. This restriction applies for Cus in all pictures.

  ![Fig11](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig11.png)

**Figure** **11****–****Redundant splitting patterns of binary tree split and ternary tree split cases**

When the splits are prohibited as described above, signalling of the corresponding syntax elements is modified to account for the prohibited cases. For example, when any case in Figure 11 is identified (i.e. the binary split is prohibited for a CU of a central partition), the syntax element mtt_split_cu_binary_flag which specifies whether the split is a binary split or a ternary split is not signalled and is instead inferred to be equal to 0 by the decoder.

### 3.2.6    Virtual pipeline data units (VPDUs)

Virtual pipeline data units (VPDUs) are defined as non-overlapping units in a picture. In hardware decoders, successive VPDUs are processed by multiple pipeline stages at the same time. The VPDU size is roughly proportional to the buffer size in most pipeline stages, so it is important to keep the VPDU size small. In most hardware decoders, the VPDU size can be set to maximum transform block (TB) size. However, in VVC, ternary tree (TT) and binary tree (BT) partition may lead to the increasing of VPDUs size.

In order to keep the VPDU size as 64x64 luma samples, the following normative partition restrictions (with syntax signaling modification) are applied in VTM, as shown in Figure 12:

–  TT split is not allowed for a CU with either width or height, or both width and height equal to 128.

–  For a 128xN CU with N ≤ 64 (i.e. width equal to 128 and height smaller than 128), horizontal BT is not allowed.

–  For an Nx128 CU with N ≤ 64 (i.e. height equal to 128 and width smaller than 128), vertical BT is not allowed.

  ![Fig12](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig12.png)

**Figure** **12** **– Examples of** **disallowed TT and BT partitioning in VTM**

 

### 3.2.7    Intra chroma partitioning and prediction restriction

Since the dual tree in intra picture allows to apply different partitioning in the chroma coding tree compared to the luma coding tree, the dual tree introduces longer coding pipeline and the QTBT MinQTSizeC value range and MinBtSizeY and MinTTSizeY in chroma tree allow small chroma blocks such as 2x2, 4x2, and 2x4. It provides difficulties in practical decoder design. Moreover, several prediction modes such as CCLM, planar and angular mode needs multiplication. In order to alleviate the above mentioned issues, small chroma block sizes (2x2/2x4/4x2) are restricted in dual tree as a partitioning restriction. 

In typical hardware video encoders and decoders, processing throughput drops when a picture has more small intra blocks because of sample processing data dependency between neighbouring intra blocks. The predictor generation of an intra block requires top and left boundary reconstructed samples from neighbouring blocks. Therefore, intra prediction has to be sequentially processed block by block.

In HEVC, the smallest intra CU is 8x8 luma samples. The luma component of the smallest intra CU can be further split into four 4x4 luma intra prediction units (Pus), but the chroma components of the smallest intra CU cannot be further split. Therefore, the worst case hardware processing throughput occurs when 4x4 chroma intra blocks or 4x4 luma intra blocks are processed. In VVC, in order to improve worst case throughput, chroma intra CBs smaller than 16 chroma samples is disallowed by constraining the partitioning of chroma intra CBs. In single coding tree, a smallest chroma intra prediction unit (SCIPU) is defined as a coding tree node whose chroma block size is larger than or equal to 16 chroma samples and has at least one child luma block smaller than 64 luma samples. It is required that in each SCIPU, all CBs are inter, or all CBs are non-inter, i.e, either intra or intra block copy (IBC). In case of a non-inter SCIPU, it is further required that chroma of the non-inter SCIPU shall not be further split and luma of the SCIPU is allowed to be further split. In this way, the smallest chroma intra CB size is 16 chroma samples, and 2x2, 2x4, and 4x2 chroma CBs are removed. In addition, chroma scaling is not applied in case of a non-inter SCIPU. Here, no additional syntax is signalled, and whether a SCIPU is non-inter can be derived by the prediction mode of the first luma CB in the SCIPU. The type of a SCIPU is inferred to be non-inter if the current slice is an I-slice or the current SCIPU has a 4x4 luma partition in it after further split one time (because no inter 4x4 is allowed in VVC); otherwise, the type of the SCIPU (inter or non-inter) is indicated by one flag before parsing the Cus in the SCIPU. In addition, a restriction on picture size is considered to avoid 2x2/2x4/4x2 intra chroma blocks at the corner of pictures by considering the picture width and height to be multiple of max (8, MinCbSizeY). 