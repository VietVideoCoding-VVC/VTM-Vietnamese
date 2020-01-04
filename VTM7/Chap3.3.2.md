### 3.3.2     Cross-component linear model prediction

To reduce the cross-component redundancy, a cross-component linear model (CCLM) prediction mode is used in the VVC, for which the chroma samples are predicted based on the reconstructed luma samples of the same CU by using a linear model as follows:

​                                                            $pred_C(i, j) = \alpha \cdot rec_L'(i, j) + \beta$                                         (3-1)

where $pred_C(i, j)$ represents the predicted chroma samples in a CU and $rec_L(i, j)$  represents the downsampled reconstructed luma samples of the same CU. 

The CCLM parameters ($\alpha$ and $\beta$) are derived with at most four neighbouring chroma samples and their corresponding down-sampled luma samples. Suppose the current chroma block dimensions are W×H, then W’ and H’ are set as

- W’=W, H’=H when LM mode is applied;

- W’=W+H when LM-A mode is applied;

- H’=H+W when LM-L mode is applied;

The above neighbouring positions are denoted as S[0, -1]…S[W’-1, -1] and the the left neighbouring positions are denoted as S[-1, 0]…S[-1, H’-1]. Then the four samples are selected as

​	–     S[W’/4, -1], S[3W’/4, -1], S[-1, H’/4], S[-1, 3H’/4] when LM mode is applied and both above and left neighbouring samples are available;

​	–     S[W’/8, -1], S[3W’/8, -1], S[5W’/8, -1], S[7W’/8, -1] when LM-A mode is applied or only the above neighbouring samples are available;

​	–     S[-1, H’/8], S[-1, 3H’/8], S[-1, 5H’/8], S[-1, 7H’/8] when LM-L mode is applied or only the left neighbouring samples are available;

The four neighbouring luma samples at the selected positions are down-sampled and compared four times to find two smaller values: $x^0_A$ and $x^1_A$ and two larger values: $x^0_B$ and $x^0_B$. Their corresponding chroma sample values are denoted as $y^0_A, y_A^1, y_B^0, $  and $y_B^1$. Then $x_A, x_B, y_A$ and $y_B$ are derived as:

​                         $X_a=(x^0_A + x^1_A +1)>>1; X_b=(x^0_B + x^1_B +1)>>1;$

​                          $Y_a=(y^0_A + y^1_A +1)>>1; Y_b=(y^0_B + y^1_B +1)>>1$       (3-2)

Finally, the linear model parameters   and   are obtained according to the following equations.

​                                                $A= \frac{Y_a - Y_b}{X_a - X_b} $                         (3-3)

​                                                $\beta = Y_b - \alpha \cdot X_b$                 (3-4)

Figure 16 shows an example of the location of the left and above samples and the sample of the current block involved in the CCLM mode.

  ![Fig16](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig16.png)

**Figure** **16** **–** **Locations of the samples used for the derivation of α and β**

The division operation to calculate parameter   is implemented with a look-up table. To reduce the memory required for storing the table, the *diff* value (difference between maximum and minimum values) and the parameter   are expressed by an exponential notation. For example, *diff* is approximated with a 4-bit significant part and an exponent. Consequently, the table for 1/diff is reduced into 16 elements for 16 values of the significand as follows:

$DivTable [\space ] =$  {$0, 7, 6, 5, 5, 4, 4, 3, 3, 2, 2, 1, 1, 1, 1, 0$}        (3-5)

This would have a benefit of both reducing the complexity of the calculation as well as the memory size required for storing the needed tables

Besides the above template and left template can be used to calculate the linear model coefficients together, they also can be used alternatively in the other 2 LM modes, called LM_A, and LM_L modes.

In LM_A mode, only the above template are used to calculate the linear model coefficients. To get more samples, the above template are extended to (W+H). In LM_L mode, only left template are used to calculate the linear model coefficients. To get more samples, the left template are extended to (H+W).

For a non-square block, the above template are extended to W+W, the left template are extended to H+H. 

To match the chroma sample locationsfor 4:2:0 video sequences, two types of downsampling filter are applied to luma samples to achieve 2 to 1 downsampling ratio in both horizontal and vertical directions. The selection of downsampling filter is specified by a SPS level flag. The two downsmapling filters are as follows, which are corresponding to “type-0” and “type-2” content, respectively.

 $Rec_L'(i, j) =  [rec_L(2i-1, 2j-1) +2 \cdot rec_L(2i-1, 2j-1) + rec_L(2i+1, 2j-1)$

​                        $+rec_L(2i-1, 2j) + 2\cdot rec_L(2i, 2j) + rec_L(2i+1, 2j) +4] >>3$                       (3-6)

 $rec_L'(i, j) =  [rec_L(2i, 2j-1)+rec_L(2i-1, 2j) + 4\cdot rec_L(2i, 2j)$

​                         $+rec_L(2i+1, 2j) + rec_L(2i, 2j+1) + 4]>>3$                                                                 (3-7)

Note that only one luma line (general line buffer in intra prediction) is used to make the downsampled luma samples when the upper reference line is at the CTU boundary.

This parameter computation is performed as part of the decoding process, and is not just as an encoder search operation. As a result, no syntax is used to convey the $α$ and $β$ values to the decoder.

For chroma intra mode coding, a total of 8 intra modes are allowed for chroma intra mode coding. Those modes include five traditional intra modes and three cross-component linear model modes (CCLM, LM_A, and LM_L). Chroma mode signalling and derivation process are shown in Table 3‑3. Chroma mode coding directly depends on the intra prediction mode of the corresponding luma block. Since separate block partitioning structure for luma and chroma components is enabled in I slices, one chroma block may correspond to multiple luma blocks. Therefore, for Chroma DM mode, the intra prediction mode of the corresponding luma block covering the center position of the current chroma block is directly inherited. 

Table 3‑3 – Derivation of chroma prediction mode from luma mode when cclm_is enabled

| Chroma prediction mode | Corresponding luma  intra prediction mode |      |      |      |                     |
| ---------------------- | ----------------------------------------- | ---- | ---- | ---- | ------------------- |
|                        | 0                                         | 50   | 18   | 1    | X  ( 0 <= X <= 66 ) |
| 0                      | 66                                        | 0    | 0    | 0    | 0                   |
| 1                      | 50                                        | 66   | 50   | 50   | 50                  |
| 2                      | 18                                        | 18   | 66   | 18   | 18                  |
| 3                      | 1                                         | 1    | 1    | 66   | 1                   |
| 4                      | 0                                         | 50   | 18   | 1    | X                   |
| 5                      | 81                                        | 81   | 81   | 81   | 81                  |
| 6                      | 82                                        | 82   | 82   | 82   | 82                  |
| 7                      | 83                                        | 83   | 83   | 83   | 83                  |

A single binarization table is used regardless of the value of sps_cclm_enabled_flag as shown in Table 3‑4. 

Table 3‑4– Unified binarization table for chroma prediction mode

| **Value of intra_chroma_pred_mode** | **Bin string** |
| ----------------------------------- | -------------- |
| 4                                   | 00             |
| 0                                   | 0100           |
| 1                                   | 0101           |
| 2                                   | 0110           |
| 3                                   | 0111           |
| 5                                   | 10             |
| 6                                   | 110            |
| 7                                   | 111            |

In Table 3‑4, the first bin indicates whether it is regular (0) or LM modes (1). If it is LM mode, then the next bin indicates whether it is LM_CHROMA (0) or not. If it is not LM_CHROMA, next 1 bin indicates whether it is LM_L (0) or LM_A (1). For this case, when sps_cclm_enabled_flag is 0, the first bin of the binarization table for the corresponding intra_chroma_pred_mode can be discarded prior to the entropy coding. Or, in other words, the first bin is inferred to be 0 and hence not coded. This single binarization table is used for both sps_cclm_enabled_flag equal to 0 and 1 cases. The first two bins in Table 3‑4 are context coded with its own context model, and the rest bins are bypass coded.

In addition, in order to reduce luma-chroma latency in dual tree, when the 64x64 luma coding tree node is partitioned with Not Split (and ISP is not used for the 64x64 CU) or QT, the chroma CUs in 32x32 / 32x16 chroma coding tree node are allowed to use CCLM in the following way:

–  If the 32x32 chroma node is not split or partitioned QT split, all chroma CUs in the 32x32 node can use CCLM

–  If the 32x32 chroma node is partitioned with Horizontal BT, and the 32x16 child node does not split or uses Vertical BT split, all chroma Cus in the 32x16 chroma node can use CCLM.

In all the other luma and chroma coding tree split conditions, CCLM is not allowed for chroma CU.