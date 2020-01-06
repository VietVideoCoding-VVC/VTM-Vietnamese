## 1.1    Screen content coding tools

### 1.1.1    Intra block copy (IBC)

Intra block copy (IBC) is a tool adopted in HEVC extensions on SCC. It is well known that it significantly improves the coding efficiency of screen content materials. Since IBC mode is implemented as a block level coding mode, block matching (BM) is performed at the encoder to find the optimal block vector (or motion vector) for each CU. Here, a block vector is used to indicate the displacement from the current block to a reference block, which is already reconstructed inside the current picture. The luma block vector of an IBC-coded CU is in integer precision. The chroma block vector rounds to integer precision as well. When combined with AMVR, the IBC mode can switch between 1-pel and 4-pel motion vector precisions. An IBC-coded CU is treated as the third prediction mode other than intra or inter prediction modes. The IBC mode is applicable to the CUs with both width and height smaller than or equal to 64 luma samples.

At the encoder side, hash-based motion estimation is performed for IBC. The encoder performs RD check for blocks with either width or height no larger than 16 luma samples. For non-merge mode, the block vector search is performed using hash-based search first. If hash search does not return valid candidate, block matching based local search will be performed. 

In the hash-based search, hash key matching (32-bit CRC) between the current block and a reference block is extended to all allowed block sizes. The hash key calculation for every position in the current picture is based on 4x4 subblocks. For the current block of a larger size, a hash key is determined to match that of the reference block when all the hash keys of all 4×4 subblocks match the hash keys in the corresponding reference locations. If hash keys of multiple reference blocks are found to match that of the current block, the block vector costs of each matched reference are calculated and the one with the minimum cost is selected.

In block matching search, the search range is set to cover both the previous and current CTUs. 

At CU level, IBC mode is signalled with a flag and it can be signaled as IBC AMVP mode or IBC skip/merge mode as follows:

–  IBC skip/merge mode: a merge candidate index is used to indicate which of the block vectors in the list from neighboring candidate IBC coded blocks is used to predict the current block. The merge list consists of spatial, HMVP, and pairwise candidates.

–  IBC AMVP mode: block vector difference is coded in the same way as a motion vector difference. The block vector prediction method uses two candidates as predictors, one from left neighbor and one from above neighbor (if IBC coded). When either neighbor is not available, a default block vector will be used as a predictor. A flag is signaled to indicate the block vector predictor index.

#### 1.1.1.1 IBC reference region

To reduce memory consumption and decoder complexity, the IBC in VTM7 allows only the reconstructed portion of the predefined area including the region of current CTU and some region of the left CTU. Figure 54 illustrates the reference region of IBC Mode, where each block represents 64x64 luma sample unit.

​           

​          X          









​          Curr          







​          X          



​          X          





​          Curr          





​          X          





​          X          

​          X          

​          X          



​          Curr          







​          X          





​                     



​          X          





​          Curr          

​          X          











​                             





**Figure** **54** **–current CTU processing order and its available reference samples in current and left CTU** 

Depending on the location of the current coding CU location within the current CTU, the following applies:

–  If current block falls into the top-left 64x64 block of the current CTU, then in addition to the already reconstructed samples in the current CTU, it can also refer to the reference samples in the bottom-right 64x64 blocks of the left CTU, using CPR mode. The current block can also refer to the reference samples in the bottom-left 64x64 block of the left CTU and the reference samples in the top-right 64x64 block of the left CTU, using CPR mode.

–  If current block falls into the top-right 64x64 block of the current CTU, then in addition to the already reconstructed samples in the current CTU, if luma location (0, 64) relative to the current CTU has not yet been reconstructed, the current block can also refer to the reference samples in the bottom-left 64x64 block and bottom-right 64x64 block of the left CTU, using CPR mode; otherwise, the current block can also refer to reference samples in bottom-right 64x64 block of the left CTU.

–  If current block falls into the bottom-left 64x64 block of the current CTU, then in addition to the already reconstructed samples in the current CTU, if luma location (64, 0) relative to the current CTU has not yet been reconstructed, the current block can also refer to the reference samples in the top-right 64x64 block and bottom-right 64x64 block of the left CTU, using CPR mode. Otherwise, the current block can also refer to the reference samples in the bottom-right 64x64 block of the left CTU, using CPR mode.

–  If current block falls into the bottom-right 64x64 block of the current CTU, it can only refer to the already reconstructed samples in the current CTU, using CPR mode.

This restriction allows the IBC mode to be implemented using local on-chip memory for hardware implementations.

#### 1.1.1.2 IBC interaction with other coding tools

The interaction between IBC mode and other inter coding tools in VTM7, such as pairwise merge candidate, history based motion vector predictor (HMVP), combined intra/inter prediction mode (CIIP), merge mode with motion vector difference (MMVD), and triangle partition are as follows:

–  IBC can be used with pairwise merge candidate and HMVP. A new pairwise IBC merge candidate can be generated by averaging two IBC merge candidates. For HMVP, IBC motion is inserted into history buffer for future referencing. 

–  IBC cannot be used in combination with the following inter tools: affine motion, CIIP, MMVD, and triangle partition. 

–  IBC is not allowed for the chroma coding blocks when DUAL_TREE partition is used. 

Unlike in the HEVC screen content coding extension, the current picture is no longer included as one of the reference pictures in the reference picture list 0 for IBC prediction. The derivation process of motion vectors for IBC mode excludes all neighboring blocks in inter mode and vice versa. The following IBC design aspects  are applied:

–  IBC shares the same process as in regular MV merge including with pairwise merge candidate and history based motion predictor, but disallows TMVP and zero vector because they are invalid for IBC mode.

–  Separate HMVP buffer (5 candidates each) is used for conventional MV and IBC.

–  Block vector constraints are implemented in the form of bitstream conformance constraint, the encoder needs to ensure that no invalid vectors are present in the bitsream, and merge shall not be used if the merge candidate is invalid (out of range or 0). Such bitstream conformance constraint is expressed in terms of a virtual buffer as described below. 

–  For deblocking, IBC is handled as inter mode.

–  If the current block is coded using IBC prediction mode, AMVR does not use quarter-pel; instead, AMVR is signaled to only indicate whether MV is inter-pel or 4 integer-pel.

–  The number of IBC merge candidates can be signalled in the slice header separately from the numbers of regular, subblock, and triangle merge candidates. 

 

A virtual buffer concept is used to describe the allowable reference region for IBC prediction mode and valid block vectors. Denote CTU size as ctbSize, the virtual buffer, ibcBuf, has width being wIbcBuf = 128*128/ctbSize and height hIbcBuf = ctbSize. For example, for a CTU size of 128x128, the size of ibcBuf is also 128x128; for a CTU size of 64x64, the size of ibcBuf is 256x64; and a CTU size of 32x32, the size of ibcBuf is 512x32.

The size of a VPDU is min(ctbSize, 64) in each dimension, Wv = min(ctbSize, 64).

The virtual IBC buffer, ibcBuf is maintained as follows.

1)   At the beginning of decoding each CTU row, refresh the whole ibcBuf with an invalid value -1.

2)   At the beginning of decoding a VPDU (xVPDU, yVPDU) relative to the top-left corner of the picture, set the ibcBuf[ x ][ y ] = –1, with x = xVPDU%wIbcBuf, …, xVPDU% wIbcBuf + Wv – 1; y = yVPDU%ctbSize, …, yVPDU%ctbSize + Wv – 1.

3)   After decoding a CU contains (x, y) relative to the top-left corner of the picture, set

ibcBuf[ x % wIbcBuf ][ y % ctbSize ] = recSample[ x ][ y ]

For a block covering the coordinates (x, y), if the following is true for a block vector *bv = (bv[0], bv[1])*, then it is valid; otherwise, it is not valid: 

*ibcBuf[ (x + bv[0])%* *wIbcBuf**] [ (y + bv[1]) % ctbSize ] shall not be equal to* –*1.*