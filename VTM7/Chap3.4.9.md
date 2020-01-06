### 3.4.9    Bi-directional optical flow (BDOF)

The bi-directional optical flow (BDOF) tool is included in VVC. BDOF, previously referred to as BIO, was included in the JEM. Compared to the JEM version, the BDOF in VVC is a simpler version that requires much less computation, especially in terms of number of multiplications and the size of the multiplier. 

BDOF is used to refine the bi-prediction signal of a CU at the $4×4$ subblock level. BDOF is applied to a CU if it satisfies all the following conditions:

·     The CU is coded using “true” bi-prediction mode, i.e., one of the two reference pictures is prior to the current picture in display order and the other is after the current picture in display order

·     The distances (i.e. POC difference) from two reference pictures to the current picture are same

·     Both reference pictures are short-term reference pictures.

·     The CU is not coded using affine mode or the ATMVP merge mode

·     CU has more than 64 luma samples

·     Both CU height and CU width are larger than or equal to 8 luma samples

·     BCW weight index indicates equal weight

·     WP is not enabled for the current CU

·     CIIP mode is not used for the current CU

BDOF is only applied to the luma component. As its name indicates, the BDOF mode is based on the optical flow concept, which assumes that the motion of an object is smooth. For each $4×4$ subblock, a motion refinement $(v_x, v_y)$  is calculated by minimizing the difference between the L0 and L1 prediction samples. The motion refinement is then used to adjust the bi-predicted sample values in the $4x4$ subblock. The following steps are applied in the BDOF process. 

First, the horizontal and vertical gradients, $\frac{\partial I^{(k)}}{\partial x}(i, j)$ and $\frac{\partial I^{(k)}}{\partial y}(i, j)$, $k = 0, 1$, of the two prediction signals are computed by directly calculating the difference between two neighboring samples, i.e.,

​                          $ \left\{ \begin{array}{ll} \frac{\partial I^{(k)}}{\partial x}(i, j) =  \left(I^{(k)}(i+1, j) \gg shift1 \right) - \left(I^{(k)}(i-1, j) \gg shift1 \right)  \\ \frac{\partial I^{(k)}}{\partial y}(i, j) = \left(I^{(k)}(i, j + 1) \gg shift1\right) - (I^{(k)}(i, j - 1) \gg shift1)\end{array} \right.$                      (3-25)

where $I^{(k)(i,j)}$ are the sample value at coordinate $(i, j)$ of the prediction signal in list $k, k= 0, 1$, and $shift1$ is calculated based on the luma bit depth, $bitDepth$, as $shift1 = \text{max}( 6, bitDepth-6)$.

Then, the auto- and cross-correlation of the gradients,  $S_1, S_2, S_3, S_5$ and $S_6$, are calculated as

​                          $ \left\{ \begin{array}{ll} S_1 = \sum_{(i, j)\in \Omega } Abs(\psi_x(i, j)), \space \space\space\space\space\space\space\space S_3 = \sum_{(i, j) \in \Omega } \theta(i, j) \cdot Sign(\psi_x(i, j)),  \\ S_5 = \sum_{(i, j)\in \Omega } Abs(\psi_y(i, j)), \space \space\space\space\space\space\space\space S_6 = \sum_{(i, j)\in \Omega } \theta(i, j) \cdot Sign(\psi_y(i, j)), \\ S_2 = \sum_{(i, j) \in \Omega} \psi_x(i, j) \cdot Sign(\psi_y(i, j))  \end{array} \right.$             (3-26)

where

​                                $ \left\{ \begin{array}{ll} \psi_x(i, j) = \left(\frac{\partial I^{(1)}}{\partial x} (i, j) + \frac{\partial I^{(0)}}{\partial x} (i, j) \right) \gg n_a ,  \\ \psi_y(i, j) = \left(\frac{\partial I^{(1)}}{\partial y} (i, j) + \frac{\partial I^{(0)}}{\partial y} (i, j) \right) \gg n_a , \\ \theta(i, j) = (I^{(1)}(i, j) \gg n_b)) - (I^{(0)})(i, j)\gg n_b) \end{array} \right.$                                                  (3-27)

where $\Omega$ is a $6×6$ window around the $4×4$ subblock, and the values of $n_a$ and $n_b$ are set equal to $\text{min}( 1, bitDepth - 11 )$ and $\text{min}( 4, bitDepth – 8 )$, respectively.

The motion refinement $(v_x, v_y)$ is then derived using the cross- and auto-correlation terms using the following: 

   $ \left\{ \begin{array}{ll} v_x = S_1 > 0 \space ? \space \text{clip3}\Bigg( -th'_{BIO}, th'_{BIO}, -\Big( \large(S_3\cdot 2^{n_b - n_a} \large) \gg \big \lfloor \text{log}_2 S_1 \big \rfloor  \Big) \Bigg) \space : \space 0, \\  v_y = S_5 > 0 \space ? \space \text{clip3}\Bigg( -th'_{BIO}, th'_{BIO}, -\bigg( \Big( S_6\cdot 2^{n_b - n_a} -  \Big( (v_x S_{2, m}) \ll n_{s_2} + v_x S_{2,s} \Big) / 2 \bigg) \gg \big \lfloor \text{log}_2 S_5 \big \rfloor \Bigg) \space : \space 0 \end{array} \right.$           (3-28)

where $S_{2, m}=S_2 \gg n_{S_2}, S_{2, s} = S_2 \&(2^{n_{S_2}} - 1), th'_{BIO}=2^{\text{max}(5, BD-7)}$.  Notation $\lfloor \cdot \rfloor$  is the floor function, and  $n_{S_2} = 12$.

Based on the motion refinement and the gradients, the following adjustment is calculated for each sample in the $4×4$ subblock:

​                      $b(x, y) = rnd \Bigg( \bigg( v_x \Big( \frac{\partial I^{(1)}(x, y)}{\partial x} - \frac{\partial I^{(0)}(x, y)}{\partial x}\Big) + v_y \Big( \frac{\partial I^{(1)}(x, y)}{\partial y} - \frac{\partial I^{(0)}(x, y)}{\partial y}\Big) \bigg )/2 \Bigg)$             (3-29)

Finally, the BDOF samples of the CU are calculated by adjusting the bi-prediction samples as follows: 

​                     $pred_{BDOF}(x, y) = \Big( I^{(0)}(x, y) + I^{(1)}(x, y) + b(x, y) + o_{offset} \Big) \gg shift $                     (3-30)

These values are selected such that the multipliers in the BDOF process do not exceed 15-bit, and the maximum bit-width of the intermediate parameters in the BDOF process is kept within 32-bit. 

In order to derive the gradient values, some prediction samples $I^{(k)}(i, j)$  in list $k$ $(k = 0, 1)$ outside of the current CU boundaries need to be generated. As depicted in Figure 35, the BDOF in VVC uses one extended row/column around the CU’s boundaries. In order to control the computational complexity of generating the out-of-boundary prediction samples, prediction samples in the extended area (white positions) are generated by taking the reference samples at the nearby integer positions (using floor() operation on the coordinates) directly without interpolation, and the normal $8$-tap motion compensation interpolation filter is used to generate prediction samples within the CU (gray positions). These extended sample values are used in gradient calculation only. For the remaining steps in the BDOF process, if any sample and gradient values outside of the CU boundaries are needed, they are padded (i.e. repeated) from their nearest neighbors. 

 

**Figure** **35** **– Extended CU region used in BDOF**

When the width and/or height of a CU are larger than $16$ luma samples, it will be split into subblocks with width and/or height equal to $16$ luma samples, and the subblock boundaries are treated as the CU boundaries in the BDOF process. The maximum unit size for BDOF process is limited to $16 \times 16$. For each subblock, the BDOF process could skipped. When the SAD of between the initial L0 and L1 prediction samples is smaller than a threshold, the BDOF process is not applied to the subblock. The threshold is set equal to $(8 * W*( H \gg1 )$, where W indicates the subblock width, and H indicates subblock height. To avoid the additional complexity of SAD calculation, the SAD between the initial L0 and L1 prediction samples calculated in DVMR process is re-used here.

If BCW is enabled for the current block, i.e., the BCW weight index indicates unequal weight, then bi-directional optical flow is disabled. Similarly, if WP is enabled for the current block, i.e., the `luma_weight_lx_flag` is $1$ for either of the two reference pictures, then BDOF is also disabled. When a CU is coded with symmetric MVD mode or CIIP mode, BDOF is also disabled.