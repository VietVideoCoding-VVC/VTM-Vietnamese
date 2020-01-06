### 3.5.2    Multiple transform selection (MTS) for core transform

In addition to DCT-II which has been employed in HEVC, a Multiple Transform Selection (MTS) scheme is used for residual coding both inter and intra coded blocks. It uses multiple selected transforms from the DCT8/DST7. The newly introduced transform matrices are DST-VII and DCT-VIII. Table 3‑10  shows the basis functions of the selected DST/DCT.

Table 3‑10 - Transform basis functions of DCT-II/ VIII and DSTVII for N-point input

| Transform  Type | Basis  function $T_i(j), i, j = 0,  1, \dots, N−1$           |
| --------------- | ------------------------------------------------------------ |
| DCT-II          | $T_i(j) = \omega_0 \cdot \sqrt{\frac{2}{N}} \cdot \text{cos}\bigg( \frac{\pi \cdot i \cdot (2j + 1)}{2N} \bigg)$                                                                             where,  $\omega_0 =\left \{ \begin{array}{ll} \sqrt{\frac{2}{N}} \space \space \space \space i = 0, \\ 1 \space \space\space \space\space\space\space\space\space \space i \neq 0 \end{array}  \right.$ |
| DCT-VIII        | $T_i(j) = \sqrt{\frac{4}{2N + 1}} \cdot \text{cos}\bigg( \frac{\pi \cdot (2i + 1) \cdot (2j + 1)}{4N + 2} \bigg)$ |
| DST-VII         | $T_i(j) = \sqrt{\frac{4}{2N + 1}} \cdot \text{sin}\bigg( \frac{\pi \cdot (2i + 1) \cdot (j + 1)}{2N + 1} \bigg)$ |

In order to keep the orthogonality of the transform matrix, the transform matrices are quantized more accurately than the transform matrices in HEVC. To keep the intermediate values of the transformed coefficients within the 16-bit range, after horizontal and after vertical transform, all the coefficients are to have 10-bit.

In order to control MTS scheme, separate enabling flags are specified at SPS level for intra and inter, respectively. When MTS is enabled at SPS, a CU level flag is signaled to indicate whether MTS is applied or not. Here, MTS is applied only for luma. The MTS CU level flag is signaled when the following conditions are satisfied. 

\-     Both width and height smaller than or equal to 32

\-     CBF flag is equal to one

If MTS CU flag is equal to zero, then DCT2 is applied in both directions. However, if MTS CU flag is equal to one, then two other flags are additionally signaled to indicate the transform type for the horizontal and vertical directions, respectively. Transform and signaling mapping table as shown in Table 3‑11. Unified the transform selection for ISP and implicit MTS is used by removing the intra-mode and block-shape dependencies. If current block is ISP mode or if the current block is intra block and both intra and inter explicit MTS is on, then only DST7 is used for both horizontal and vertical transform cores. When it comes to transform matrix precision, 8-bit primary transform cores are used. Therefore, all the transform cores used in HEVC are kept as the same, including 4-point DCT-2 and DST-7, 8-point, 16-point and 32-point DCT-2. Also, other transform cores including 64-point DCT-2, 4-point DCT-8, 8-point, 16-point, 32-point DST-7 and DCT-8, use 8-bit primary transform cores.

Table 3‑11 - Transform and signaling mapping table

| MTS_CU_flag | MTS_Hor_flag | MTS_Ver_flag | Intra/inter |          |
| ----------- | ------------ | ------------ | ----------- | -------- |
|             |              |              | Horizontal  | Vertical |
| 0           | -            | -            | DCT2        | DCT2     |
| 1           | 0            | 0            | DST7        | DST7     |
| 1           | 0            | 1            | DST8        | DST7     |
| 1           | 1            | 0            | DST7        | DST8     |
| 1           | 1            | 1            | DST8        | DST8     |

To reduce the complexity of large size DST-7 and DCT-8, High frequency transform coefficients are zeroed out for the DST-7 and DCT-8 blocks with size (width or height, or both width and height) equal to 32. Only the coefficients within the $16 \times 16$ lower-frequency region are retained. 

As in HEVC, the residual of a block can be coded with transform skip mode. To avoid the redundancy of syntax coding, the transform skip flag is not signaled when the CU level `MTS_CU_flag` is not equal to zero. The block size limitation for transform skip is the same to that for MTS in JEM4, which indicate that transform skip is applicable for a CU when both block width and height are equal to or less than 32. Note that implicit MTS transform is set to DCT2 when LFNST or MIP is activated for the current CU. Also the implicit MTS can be still enabled when MTS is enabled for inter coded blocks.