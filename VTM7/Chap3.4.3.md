### 3.4.3    Symmetric MVD coding

In VVC, besides the normal unidirectional prediction and bi-directional prediction mode MVD signalling, symmetric MVD mode for bi-predictional MVD signalling is applied. In the symmetric MVD mode, motion information including reference picture indices of both list-0 and list-1 and MVD of list-1 are not signaled but derived. 

The decoding process of the symmetric MVD mode is as follows:

1. At slice level, variables `BiDirPredFlag`, `RefIdxSymL0` and `RefIdxSymL1` are derived as follows:

* If `mvd_l1_zero_flag` is 1, `BiDirPredFlag` is set equal to 0.

* Otherwise, if the nearst reference picture in list-0 and the nearst reference picture in list-1 form a forward and backward pair of reference pictures or a backward and forward pair of reference pictures, `BiDirPredFlag` is set to 1, and both list-0 and list-1 reference pictures are short-term reference pictures. Otherwise `BiDirPredFlag` is set to 0. 

\2.    At CU level, a symmetrical mode flag indicating whether symmetrical mode is used or not is explicitly signaled if the CU is bi-prediction coded and `BiDirPredFlag` is equal to 1. 

When the symmetrical mode flag is true, only `mvp_l0_flag`, `mvp_l1_flag` and MVD0 are explicitly signaled. The reference indices for list-0 and list-1 are set equal to the pair of reference pictures, respectively. MVD1 is set equal to ( – MVD0 ). The final motion vectors are shown in below formula.

​                              $
​    \left\{
​                \begin{array}{ll}
​                  (mvx_0, mvy_0) = (mvpx_0+mvdx_0, mvpy_0 + mvdy_0) \\
​                  (mvx_1, mvy_1) = (mvpx_1+mvdx_1, mvpy_1 + mvdy_1)
​                \end{array}
​              \right.
  $                         (3-14)

 ![Fig26](\imgs\Fig26.PNG)

**Figure** **26** **–** **Illustration for symmetrical MVD mode**



In the encoder, symmetric MVD motion estimation starts with initial MV evaluation. A set of initial MV candidates comprising of the MV obtained from uni-prediction search, the MV obtained from bi-prediction search and the MVs from the AMVP list. The one with the lowest rate-distortion cost is chosen to be the initial MV for the symmetric MVD motion search.