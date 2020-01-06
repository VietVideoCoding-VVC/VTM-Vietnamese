### 3.4.11    Triangle partition for inter prediction

In VVC, a triangle partition mode is supported for inter prediction. The triangle partition mode is only applied to CUs that are $8 \times 8$ or larger. The triangle partition mode is signaled using a CU-level flag as one kind of merge mode, with other merge modes including the regular merge mode, the MMVD mode, the CIIP mode and the subblock merge mode.

When this mode is used, a CU is split evenly into two triangle-shaped partitions, using either the diagonal split or the anti-diagonal split (Figure 37). Each triangle partition in the CU is inter-predicted using its own motion; only uni-prediction is allowed for each partition, that is, each partition has one motion vector and one reference index. The uni-prediction motion constraint is applied to ensure that same as the conventional bi-prediction, only two motion compensated prediction are needed for each CU. The uni-prediction motion for each partition is derived using the process described in 3.4.11.1. 

​                               ![Fig37](\imgs\Fig37.png)

**Figure** **37** **– Triangle partition based inter prediction**

If triangle partition mode is used for the current CU, then a flag indicating the direction of the triangle partition (diagonal or anti-diagonal), and two merge indices (one for each partition) are further signaled. The number of maximum TPM candidate size is signaled explicitly at slice level and specifies syntax binarization for TMP merge indices. After predicting each of the triangle partitions, the sample values along the diagonal or anti-diagonal edge are adjusted using a blending processing with adaptive weights. This is the prediction signal for the whole CU, and transform and quantization process will be applied to the whole CU as in other prediction modes. Finally, the motion field of a CU predicted using the triangle partition mode is stored as in 3.4.11.3. 

The triangle partition mode is not used in combination with SBT, that is, when the signaled triangle mode is equal to $1$, the `cu_sbt_flag` is inferred to be 0 without signaling. 

#### 1.1.1.1 Uni-prediction candidate list construction

The uni-prediction candidate list is derived directly from the merge candidate list constructed according to the extended merge prediction process in 3.4.1. Denote n as the index of the uni-prediction motion in the triangle uni-prediction candidate list. The LX motion vector of the $n$-th extended merge candidate, with $X$ equal to the parity of $n$, is used as the n-th uni-prediction motion vector for triangle partition mode. These motion vectors are marked with “$x$” in Figure 38. In case a corresponding $LX$ motion vector of the n-the extended merge candidate does not exist, the $L(1-X)$ motion vector of the same candidate is used instead as the uni-prediction motion vector for triangle partition mode. 

 ![Fig38](\imgs\Fig38.png)

**Figure** **38** **– Uni-prediction MV selection for triangle partition mode** 

#### 1.1.1.2 Blending along the triangle partition edge

After predicting each triangle partition using its own motion, blending is applied to the two prediction signals to derive samples around the diagonal or anti-diagonal edge. The following weights are used in the blending process:

* $\{7/8, 6/8, 5/8, 4/8, 3/8, 2/8, 1/8 \}$ for luma and $\{6/8, 4/8, 2/8\}$ for chroma, as shown in Figure 39.

 ![Fig39](imgs\Fig39.png)

**Figure** **39** **– weights used in the blending process** 

#### 1.1.1.3 Motion field storage

The motion vectors of a CU coded in triangle partition mode are generated using the following process:

1)   If Mv1 and Mv2 are from different reference picture lists (one from L0 and the other from L1), then Mv1 and Mv2 are simply combined to form the bi-prediction motion vector.

2)   Otherwise, if Mv1 and Mv2 are from the same list, only uni-prediction motion Mv2 is stored.