### 5.3.5    Quantization

#### 5.3.5.1 Quantization parameter control 

In VVC Draft 6, Maximum QP was extended from 51 to 63, and the signaling of initial QP was changed accordingly. The initial value of SliceQpY is modified at the slice segment layer when a non-zero value of slice_qp_delta is coded. Specifically, the value of init_qp_minus26 is modified to be in the range of ( − 26 + QpBdOffsetY ) to +37. When the size of a transform block is not a power of 4, the transform coefficients are processed along with a modification to the QP or QP levelScale table rather than by multiplication by 181/256 (or 181/128), to compensate for an implicit scaling by the transform process. For transform skip block, minimum allowed Quantization Parameter (QP) is defined as 4 because quantization step size becomes 1 when QP is equal to 4.

In HEVC (and also in H.264), a fixed look-up table is used to convert the luma quantization parameter QPY to chroma quantization parameter QPC. In VVC, a more flexible luma-to-chroma QP mapping is used. Instead of having a fixed table, the luma-to-chroma QP mapping relationship is signalled in the SPS using a flexible piecewise linear model, with the only constraint on the linear model being that the slope of each piece cannot be negative (i.e., as luma QP increases, chroma QP must stay flat or increase, but cannot decrease). The piecewise linear model is defined by: 1) number of pieces in the model; 2) input (luma) and output (chroma) delta QPs for that piece. The input range of the piecewise linear model is [−QpBdOffsetY, 63] and the output range of the piecewise linear model is [−QpBdOffsetC, 63]. The QP mapping relationship can be signalled separately for Cb, Cr and joint Cb/Cr coding, or signalled jointly for all three types of residual coding. 

Same as in HEVC, CU-level QP adaptation is allowed in VVC. Delta QP values for luma and chroma components can be signalled separately. For the chroma components, the allowed chroma QP offset values are signalled in the form of offset lists in the PPS in a similar manner as in HEVC. The lists are defined separately for Cb, Cr and joint Cb/Cr coding. Up to 6 offset values are allowed for each of Cb, Cr, and joint Cb/Cr lists. At the CU-level, an index is signalled to indicate which one of the offset values in the offset list is used to adjust the chroma QP for that CU. 

#### 5.3.5.2 Dependent quantization 

In addition, the same HEVC scalar quantization is used with a new concept called dependent scalar quantization. Dependent scalar quantization refers to an approach in which the set of admissible reconstruction values for a transform coefficient depends on the values of the transform coefficient levels that precede the current transform coefficient level in reconstruction order. The main effect of this approach is that, in comparison to conventional independent scalar quantization as used in HEVC, the admissible reconstruction vectors are packed denser in the N-dimensional vector space (N represents the number of transform coefficients in a transform block). That means, for a given average number of admissible reconstruction vectors per N-dimensional unit volume, the average distortion between an input vector and the closest reconstruction vector is reduced. The approach of dependent scalar quantization is realized by: (a) defining two scalar quantizers with different reconstruction levels and (b) defining a process for switching between the two scalar quantizers.

 

​                               

Figure 43 – Illustration of the two scalar quantizers used in the proposed approach of dependent quantization.

The two scalar quantizers used, denoted by Q0 and Q1, are illustrated in Figure 43. The location of the available reconstruction levels is uniquely specified by a quantization step size Δ. The scalar quantizer used (Q0 or Q1) is not explicitly signalled in the bitstream. Instead, the quantizer used for a current transform coefficient is determined by the parities of the transform coefficient levels that precede the current transform coefficient in coding/reconstruction order.

 

 

Figure 44 – State transition and quantizer selection for the proposed dependent quantization.

 

As illustrated in Figure 44, the switching between the two scalar quantizers (Q0 and Q1) is realized via a state machine with four states. The state can take four different values: 0, 1, 2, 3. It is uniquely determined by the parities of the transform coefficient levels preceding the current transform coefficient in coding/reconstruction order. At the start of the inverse quantization for a transform block, the state is set equal to 0. The transform coefficients are reconstructed in scanning order (i.e., in the same order they are entropy decoded). After a current transform coefficient is reconstructed, the state is updated as shown in Figure 18, where k denotes the value of the transform coefficient level.

It is also supported to signal the default and user-defined scaling matrices. The DEFAULT mode scaling matrices are all flat, with elements equal to 16 for all TB sizes. IBC and intra coding modes currently share the same scaling matrices. Thus, for the case of USER_DEFINED matrices, the number of MatrixType and MatrixType_DC are updated as follows:

·     **MatrixType:** **30 = 2** (2 for intra&IBC/inter) **×** **3** (Y/Cb/Cr components) **×** **5** (square TB size: from 4×4 to 64×64 for luma, from 2×2 to 32×32 for chroma)

·     **MatrixType_DC:** **14 = 2** (2 for intra&IBC/inter × 1 for Y component) **×** **3** (TB size: 16×16, 32×32, 64×64) **+** **4** (2 for intra&IBC/inter × 2 for Cb/Cr components) **×** **2** (TB size: 16×16, 32×32)

The DC values are separately coded for following scaling matrices: 16×16, 32×32, and 64×64. For TBs of size smaller than 8×8, all elements in one scaling matrix are signalled. If the TBs have size greater than or equal to 8×8, only 64 elements in one 8×8 scaling matrix are signalled as a base scaling matrix. For obtaining square matrices of size greater than 8×8, the 8×8 base scaling matrix is up-sampled (by duplication of elements) to the corresponding square size (i.e. 16×16, 32×32, 64×64). when the zeroing-out of the high frequency coefficients for 64-point transform is applied, corresponding high frequencies of the scaling matrices are also zeroed out. That is, if the width or height of the TB is greater than or equal to 32, only left or top half of the coefficients is kept, and the remaining coefficients are assigned to zero. Moreover, the number of elements signalled for the 64×64 scaling matrix is also reduced from 8×8 to three 4×4 submatrices, since the bottom-right 4×4 elements are never used.

####  