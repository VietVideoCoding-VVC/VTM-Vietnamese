### 3.4.4    Affine motion compensated prediction 

In HEVC, only translation motion model is applied for motion compensation prediction (MCP). While in the real world, there are many kinds of motion, e.g. zoom in/out, rotation, perspective motions and the other irregular motions. In VVC, a block-based affine transform motion compensation prediction is applied. As shown Figure 27, the affine motion field of the block is described by motion information of two control point (4-parameter) or three control point motion vectors (6-parameter).

​                                ![Fig27](imgs\Fig27.png)

​                     **(a)**  **4 parameter affine model           (b) 6 parameter affine model**

**Figure** **27** **– control point based affine motion model**

For 4-parameter affine motion model, motion vector at sample location $(x, y)$ in a block is derived as:

​                                  $
​    \left\{
​                \begin{array}{ll}
​                  mv_x = \frac{mv_{1x} - mv_{0x}}{W}x+ \frac{mv_{1y} - mv_{0y}}{W}y + mv_{0x}\\
​                  mv_y = \frac{mv_{1y} - mv_{0y}}{W}x+ \frac{mv_{1y} - mv_{0x}}{W}y + mv_{0y}
​                \end{array}
​              \right.
  $                                                (3-15)

For 6-parameter affine motion model, motion vector at sample location $(x, y)$ in a block is derived as:

​                                 $
​    \left\{
​                \begin{array}{ll}
​                  mv_x = \frac{mv_{1x} - mv_{0x}}{W}x+ \frac{mv_{2x} - mv_{0x}}{H}y + mv_{0x}\\
​                  mv_y = \frac{mv_{1y} - mv_{0y}}{W}x+ \frac{mv_{2y} - mv_{0y}}{H}y + mv_{0y}
​                \end{array}
​              \right.
  $                                                (3-16)

Where $(mv_{0x}, mv_{0y})$ is motion vector of the top-left corner control point, $(mv_{1x}, mv_{1y})$ is motion vector of the top-right corner control point, and $(mv_{2x}, mv_{2y})$ is motion vector of the bottom-left corner control point.

In order to simplify the motion compensation prediction, block based affine transform prediction is applied. To derive motion vector of each $4 \times 4$ luma subblock, the motion vector of the center sample of each subblock, as shown in Figure 28, is calculated according to above equations, and rounded to $1/16$ fraction accuracy. Then the motion compensation interpolation filters are applied to generate the prediction of each subblock with derived motion vector. The subblock size of chroma-components is also set to be $4 \times 4$. The MV of a $4 \times 4$ chroma subblock is calculated as the average of the MVs of the four corresponding $4 \times 4$ luma subblocks.

 ![Fig28](imgs\Fig28.png)

Figure 28 – Affine MVF per subblock

As done for translational motion inter prediction, there are also two affine motion inter prediction modes: affine merge mode and affine AMVP mode.

#### 3.4.4.1 Affine merge prediction

AF_MERGE mode can be applied for CUs with both width and height larger than or equal to 8. In this mode the CPMVs of the current CU is generated based on the motion information of the spatial neighboring CUs. There can be up to five CPMVP candidates and an index is signaled to indicate the one to be used for the current CU. The following three types of CPVM candidate are used to form the affine merge candidate list:

1. Inherited affine merge candidates that extrapolated from the CPMVs of the neighbour CUs

2. Constructed affine merge candidates CPMVPs that are derived using the translational MVs of the neighbour CUs

3. Zero MVs

In VVC, there are maximum two inherited affine candidates, which are derived from affine motion model of the neighboring blocks, one from left neighboring CUs and one from above neighboring CUs. The candidate blocks are shown in Figure 29. For the left predictor, the scan order is $A0 \rightarrow A1$, and for the above predictor, the scan order is $B0\rightarrow B1\rightarrow B2$. Only the first inherited candidate from each side is selected. No pruning check is performed between two inherited candidates. When a neighboring affine CU is identified, its control point motion vectors are used to derived the CPMVP candidate in the affine merge list of the current CU. As shown in , if the neighbour left bottom block A is coded in affine mode, the motion vectors $v_2 , v_3$  and $v_4$ of the top left corner, above right corner and left bottom corner of the CU which contains the block A are attained. When block A is coded with 4-parameter affine model, the two CPMVs of the current CU are calculated according to  $v_2$ and $v_3$. In case that block A is coded with 6-parameter affine model, the three CPMVs of the current CU are calculated according to  $v_2 , v_3$, and $v_4$. 

 ![Fig29](\imgs\Fig29.PNG)

Figure 29 – Locations of inherited affine motion predictors

   ![Fig30](\imgs\Fig30.png)
 **Figure** **30** **–** **Control point motion vector inheritance**

Constructed affine candidate means the candidate is constructed by combining the neighbor translational motion information of each control point. The motion information for the control points is derived from the specified spatial neighbors and temporal neighbor shown in Figure 31. $\text{CPMV}_k (k=1, 2, 3, 4)$ represents the $k$-th control point. For $\text{CPMV}_1$, the $B2\rightarrow B3 \rightarrow A2$ blocks are checked and the MV of the first available block is used. For $\text{CPMV}_2$, the $B1 \rightarrow B0$ blocks are checked and for $\text{CPMV}_3$, the $A1 \rightarrow A0$ blocks are checked. For TMVP is used as $\text{CPMV}_4$ if it’s available.

After MVs of four control points are attained, affine merge candidates are constructed based on those motion information. The following combinations of control point MVs are used to construct in order:

$ \begin{array}{ll} \{\text{CPMV}_1, \text{CPMV}_2, \text{CPMV}_3\}, \space \{\text{CPMV}_1, \text{CPMV}_2, \text{CPMV}_4\}, \space \{\text{CPMV}_1, \text{CPMV}_3, \text{CPMV}_4\}, \\
 \{\text{CPMV}_2, \text{CPMV}_3, \text{CPMV}_4 \}, \space \{ \text{CPMV}_1, \text{CPMV}_2\}, \space\space\space\space\space\space\space\space\space\space\space\space\space\space\space\space\space \{ \text{CPMV}_1, \text{CPMV}_3\} \end{array} $

The combination of 3  $\text{CPMV}$s constructs a 6-parameter affine merge candidate and the combination of 2  $\text{CPMV}$s constructs a 4-parameter affine merge candidate. To avoid motion scaling process, if the reference indices of control points are different, the related combination of control point MVs is discarded.

 ![Fig31](\imgs\Fig31.PNG)

**Figure** **31** **–Locations of** **Candidates position for constructed affine merge mode**

After inherited affine merge candidates and constructed affine merge candidate are checked, if the list is still not full, zero MVs are inserted to the end of the list.



#### 3.4.4.2 Affine AMVP prediction

Affine AMVP mode can be applied for CUs with both width and height larger than or equal to 16. An affine flag in CU level is signaled in the bitstream to indicate whether affine AMVP mode is used and then another flag is signaled to indicate whether 4-parameter affine or 6-parameter affine. In this mode, the difference of the CPMVs of current CU and their predictors CPMVPs is signaled in the bitstream. The affine AVMP candidate list size is 2 and it is generated by using the following four types of CPVM candidate in order:/

1. Inherited affine AMVP candidates that extrapolated from the CPMVs of the neighbor CUs

2. Constructed affine AMVP candidates CPMVPs that are derived using the translational MVs of the neighbor CUs

3. Translational MVs from neighboring CUs

4. Zero MVs

The checking order of inherited affine AMVP candidates is same to the checking order of inherited affine merge candidates. The only difference is that, for AVMP candidate, only the affine CU that has the same reference picture as in current block is considered. No pruning process is applied when inserting an inherited affine motion predictor into the candidate list.

Constructed AMVP candidate is derived from the specified spatial neighbors shown in Figure 31. The same checking order is used as done in affine merge candidate construction. In addition, reference picture index of the neighboring block is also checked. The first block in the checking order that is inter coded and has the same reference picture as in current CUs is used. There is only one When the current CU is coded with 4-parameter affine mode, and  $mv_0$ and $mv_1$ are both available, they are added as one candidate in the affine AMVP list. When the current CU is coded with 6-parameter affine mode, and all three CPMVs are available, they are added as one candidate in the affine AMVP list. Otherwise, constructed AMVP candidate is set as unavailable.

If affine AMVP list candidates is still less than 2 after inherited affine AMVP candidates and Constructed AMVP candidate are checked,  $mv_0, mv_1$ and $mv_2$ will be added, in order, as the translational MVs to predict all control point MVs of the current CU, when available. Finally, zero MVs are used to fill the affine AMVP list if it is still not full.

 

#### 3.4.4.3 Affine motion information storage

In VVC, the CPMVs of affine CUs are stored in a separate buffer. The stored CPMVs are only used to generate the inherited CPMVPs in affine merge mode and affine AMVP mode for the lately coded CUs. The subblock MVs derived from CPMVs are used for motion compensation, MV derivation of merge/AMVP list of translational MVs and de-blocking.

To avoid the picture line buffer for the additional CPMVs, affine motion data inheritance from the CUs from above CTU is treated differently to the inheritance from the normal neighboring CUs. If the candidate CU for affine motion data inheritance is in the above CTU line, the bottom-left and bottom-right subblock MVs in the line buffer instead of the CPMVs are used for the affine MVP derivation. In this way, the CPMVs are only stored in local buffer. If the candidate CU is 6-parameter affine coded, the affine model is degraded to 4-parameter model. As shown in Figure 32, along the top CTU boundary, the bottom-left and bottom right subblock motion vectors of a CU are used for affine inheritance of the CUs in bottom CTUs.

 ![Fig32](imgs\Fig32.png)

Figure 32 – Illustration of motion vector usage for proposed combined method



#### 3.4.4.4 Prediction refinement with optical flow for affine mode

Subblock based affine motion compensation can save memory access bandwidth and reduce computation complexity compared to pixel based motion compensation, at the cost of prediction accuracy penalty. To achieve a finer granularity of motion compensation, prediction refinement with optical flow (PROF) is used to refine the subblock based affine motion compensated prediction without increasing the memory access bandwidth for motion compensation. In VVC, after the subblock based affine motion compensation is performed, luma prediction sample is refined by adding a difference derived by the optical flow equation. The PROF is described as following four steps:

Step 1) The subblock-based affine motion compensation is performed to generate subblock prediction   .

Step2) The spatial gradients  $g_x(i, j)$ and $g_y(i, j) $ of the subblock prediction are calculated at each sample location using a 3-tap filter $[-1, 0, 1]$. The gradient calculation is exactly the same as gradient calculation in BDOF.

​                       $g_x(i,j)=(I(i+1, j) \gg shift1) - (I(i-1, j) \gg shift1)$                                     (3-17)

​                       $g_y(i,j)=(I(i, j+1) \gg shift1) - (I(i, j-1) \gg shift1)$                                     (3-18)

$shift1$  is used to control the gradient’s precision. The subblock (i.e. $4 \times 4$) prediction is extended by one sample on each side for the gradient calculation. To avoid additional memory bandwidth and additional interpolation computation, those extended samples on the extended borders are copied from the nearest integer pixel position in the reference picture.

Step 3) The luma prediction refinement is calculated by the following optical flow equation.

​                                 $\Delta I(i, j)=g_x(i,j)*\Delta v_x(i, j) + g_y(i, j) * \Delta v_y(i, j)$                                        (3-19)

where the $\Delta v(i, j)$ is the difference between sample MV computed for sample location $(i, j)$, denoted by $v(i, j)$, and the subblock MV of the subblock to which sample $(i, j)$ belongs, as shown in Figure 33. The $\Delta v(i, j)$  is quantized in the unit of $1/32$ luma sample precision.

 ![Fig33](\imgs\Fig33.png)

**Figure 33 – Subblock MV $V_{SB}$ and pixel  $\Delta v(i,j)$ (red arrow)**

Since the affine model parameters and the sample location relative to the subblock center are not changed from subblock to subblock, $\Delta v(i, j)$ can be calculated for the first subblock, and reused for other subblocks in the same CU. Let $dx(i, j)$ and $dy(i, j)$ be the horizontal and vertical offset from the sample location $(i, j$ to the center of the subblock $x_{SB}, y_{SB}$,  $\Delta v(i, j)$ can be derived by the following equation,

​                                                      $
​    \left\{
​                \begin{array}{ll}
​                  dx(i, j) = i - x_{SB} \\
​                  dy(i, j) = j - y_{SB}
​                \end{array}
​              \right.
  $                                                                      (3-20)

​                                                      $
​    \left\{
​                \begin{array}{ll}
​                  \Delta v_x(i, j) = C * dx(i, j) + D*dy(i, j) \\
​                  \Delta v_y(i, j) = E*dx(i, j) + F* dy(i,j)
​                \end{array}
​              \right.
  $                                   (3-21)

In order to keep accuracy, the enter of the subblock $(x_{SB}, y_{SB})$ is calculated as $((W_{SB}-1)/2, (H_{SB}-1)/2)$, where $W_{SB}$ and $H_{SB}$ are the subblock width and height, respectively.

For 4-parameter affine model,

​                                                      $
​    \left\{
​                \begin{array}{ll}
​                  C = F = \frac{v_{1x} - v_{0x}}{w} \\
​                  E = -D = \frac{v_{1y} - v{0y}}{w}
​                \end{array}
​              \right.
  $                                                                    (3-22)

For 6-parameter affine model,

​                                                      $
​    \left\{
​                \begin{array}{ll}
​                  C =  \frac{v_{1x} - v_{0x}}{w} \\ D= \frac{v_{2x} - v_{0y}}{h} \\  
​                  E = \frac{v_{1y} - v_{0y}}{h} \\ F = \frac{v_{2y} - v_{0y}}{h}
​             \end{array}  \right.
  $                                                                                 (3-23)

where $(v_{0x}, v_{0y}), (v_{1x}, v_{1y}), (v_{2x}, v_{2y})$ are the top-left, top-right and bottom-left control point motion vectors,  $w$ and $h$ are the width and height of the CU.

Step 4) Finally, the luma prediction refinement $\Delta I(i,j)$ is added to the subblock prediction $I(i,j)$. The final prediction $I’$ is generated as the following equation.

​                                                          $I'(i,j) = I(i,j) + \Delta I(i,j)$

PROF is not be applied in two cases for an affine coded CU: 1) all control point MVs are the same, which indicates the CU only has translational motion; 2) the affine motion parameters are greater than a specified limit because the subblock based affine MC is degraded to CU based MC to avoid large memory access bandwidth requirement.

A fast encoding method is applied to reduce the encoding complexity of affine motion estimation with PROF. PROF is not applied at affine motion estimation stage in following two situations: a) if this CU is not the root block and its parent block does not select the affine mode as its best mode, PROF is not applied since the possibility for current CU to select the affine mode as best mode is low; b) if the magnitude of four affine parameters (C, D, E, F) are all smaller than a predefined threshold and the current picture is not a low delay picture, PROF is not applied because the improvement introduced by PROF is small for this case. In this way, the affine motion estimation with PROF can be accelerated.