## 1.1    Adaptive color transform

In HEVC SCC extension, adaptive color transform (ACT) was applied to reduce the redundancy between three color components in 444 chroma format. The ACT is also adopted into the VVC standard to enhance the coding efficiency of 444 chroma format coding. Same as in HEVC SCC, the ACT performs in-loop color space conversion in the prediction residual domain by adaptively converting the residuals from the input color space to YCgCo space. Figure 58 illustrates the decoding flowchart with the ACT being applied. Two color spaces are adaptively selected by signaling one ACT flag at CU level. When the flag is equal to one, the residuals of the CU are coded in the YCgCo space; otherwise, the residuals of the CU are coded in the original color space. Additionally, same as the HEVC ACT design, for inter and IBc CUs, the ACT is only enabled when there is at least one non-zero coefficient in the CU. For intra CUs, the ACT is only enabled when chroma components select the same intra prediction mode of luma component, i.e., DM mode.

​                               

Figure 58: Decoding flowchart with ACT

### 1.1.1    ACT mode

Same as in the HEVC, the color space conversions of the ACT in the VVC are based on YCgCo transform matrices. Specifically, the following forward and inverse YCgCo color transform matrices, as described as follows, is applied.

|      | (3-79) |
| ---- | ------ |
|      |        |

As shown above, the ACT transform matrices are not normalized. To compensate the dynamic range change of residuals signals before and after color transform, the QP adjustments of (–5, –5, –3) are applied to the transform residuals of Y, Cg and Co components, respectively. The adjusted quantization parameter only affects the quantization and inverse quantization of the residuals in the CU. For other coding processes (such as deblocking), original QP is still applied.

Additionally, because the forward and inverse color transforms need to access the residuals of all three components, the ACT mode is always disabled for separate-tree partition and ISP mode where the prediction block size of different color component is different.