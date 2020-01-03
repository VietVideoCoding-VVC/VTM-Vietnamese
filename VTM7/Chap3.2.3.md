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

