### 3.4.10    Decoder side motion vector refinement (DMVR)

In order to increase the accuracy of the MVs of the merge mode, a bilateral-matching based decoder side motion vector refinement is applied in VVC. In bi-prediction operation, a refined MV is searched around the initial MVs in the reference picture list L0 and reference picture list L1. The BM method calculates the distortion between the two candidate blocks in the reference picture list L0 and list L1. As illustrated in Figure 36, the SAD between the red blocks based on each MV candidate around the initial MV is calculated. The MV candidate with the lowest SAD becomes the refined MV and used to generate the bi-predicted signal.

​                               

**Figure** **36** **–** **Decoding side motion vector refinement**

In VVC, the DMVR can be applied for the CUs which are coded with following modes and features:

* CU level merge mode with bi-prediction MV

* One reference picture is in the past and another reference picture is in the future with respect to the current picture

* The distances (i.e. POC difference) from two reference pictures to the current picture are same

* Both reference pictures are short-term reference pictures

* CU has more than $64$ luma samples

* Both CU height and CU width are larger than or equal to $8$ luma samples

* BCW weight index indicates equal weight

* WP is not enabled for the current block

* CIIP mode is not used for the current block

The refined MV derived by DMVR process is used to generate the inter prediction samples and also used in temporal motion vector prediction for future pictures coding. While the original MV is used in deblocking process and also used in spatial motion vector prediction for future CU coding.

The additional features of DMVR are mentioned in the following sub-clauses.

#### 3.4.10.1 Searching scheme

In DVMR, the search points are surrounding the initial MV and the MV offset obey the MV difference mirroring rule. In other words, any points that are checked by DMVR, denoted by candidate MV pair ($MV0, MV1$) obey the following two equations:

​                                                 $MV0'=MV0+MV_{offset}$                                                               (3-31)

​                                                 $MV1' = MV1 - MV_{offset}$                                                               (3-32)

Where $MV_{offset}$ represents the refinement offset between the initial MV and the refined MV in one of the reference pictures. The refinement search range is two integer luma samples from the initial MV. The searching includes the integer sample offset search stage and fractional sample refinement stage.

$25$ points full search is applied for integer sample offset searching. The SAD of the initial MV pair is first calculated. If the SAD of the initial MV pair is smaller than a threshold, the integer sample stage of DMVR is terminated. Otherwise SADs of the remaining $24$ points are calculated and checked in raster scanning order. The point with the smallest SAD is selected as the output of integer sample offset searching stage. To reduce the penalty of the uncertainty of DMVR refinement, it is proposed to favor the original MV during the DMVR process. The SAD between the reference blocks referred by the initial MV candidates is decreased by $1/4$ of the SAD value.

The integer sample search is followed by fractional sample refinement. To save the calculational complexity, the fractional sample refinement is derived by using parametric error surface equation, instead of additional search with SAD comparison. The fractional sample refinement is conditionally invoked based on the output of the integer sample search stage. When the integer sample search stage is terminated with center having the smallest SAD in either the first iteration or the second iteration search, the fractional sample refinement is further applied.

In parametric error surface based sub-pixel offsets estimation, the center position cost and the costs at four neighboring positions from the center are used to fit a 2-D parabolic error surface equation of the following form

​                                             $E(x, y) = A(x-x_{min})^2 + B (y-y_{min}^2 +C$                                       (3-33)

where $(x_{min}, y_{min})$  corresponds to the fractional position with the least cost and $C$ corresponds to the minimum cost value. By solving the above equations by using the cost value of the five search points, the $(x_{min}, y_{min})$ is computed as:

​                        $x_{min} = \Big(E(-1, 0) -E(1, 0)\Big) / \Big( 2 \big(E(-1, 0) + E(1, 0) - 2E(0, 0) \big) \Big)$                 (3-34)

​                        $y_{min} = \Big(E(0, -1) -E(0, 1)\Big) / \Big( 2 \big(E(0, -1) + E(0, 1) - 2E(0, 0) \big) \Big)$                 (3-35)

The value of $x_min$ and $y_min$ are automatically constrained to be between $–8$ and $8$ since all cost values are positive and the smallest value is $E(0, 0)$. This corresponds to half peal offset with $1/16$th-pel MV accuracy in VVC. The computed fractional $(x_{min}, y_{min})$ are added to the integer distance refinement MV to get the sub-pixel accurate refinement delta MV.

#### 3.4.10.2 Bilinear-interpolation and sample padding

In VVC, the resolution of the MVs is $1/16$ luma samples. The samples at the fractional position are interpolated using a 8-tap interpolation filter. In DMVR, the search points are surrounding the initial fractional-pel MV with integer sample offset, therefore the samples of those fractional position needs to be interpolated for DMVR search process. To reduce the calculation complexity, the bi-linear interpolation filter is used to generate the fractional samples for the searching process in DMVR. Another important effect is that by using bi-linear filter is that with $2$-sample search range, the DVMR does not access more reference samples compared to the normal motion compensation process. After the refined MV is attained with DMVR search process, the normal $8$-tap interpolation filter is applied to generate the final prediction. In order to not access more reference samples to normal MC process, the samples, which is not needed for the interpolation process based on the original MV but is needed for the interpolation process based on the refined MV, will be padded from those available samples.

#### 3.4.10.3 Maximum DMVR processing unit

When the width and/or height of a CU are larger than $16$ luma samples, it will be further split into subblocks with width and/or height equal to $16$ luma samples. The maximum unit size for DMVR searching process is limit to $16 \times 16$.