### 1.1.1    ACT fast encoding algorithms

To avoid brutal R-D search in both the original and converted color spaces, the following fast encoding algorithms are applied in the VTM reference software to reduce the encoder complexity when the ACT is enabled.

·     The order of RD checking of enabling/disabling ACT is dependent on the original color space of input video. For RGB videos, the RD cost of ACT mode is checked first; for YCbCr videos, the RD cost of non-ACT mode is checked first. The RD cost of the second color space is checked only if there is at least one non-zero coefficient in the first color space.

·     The same ACT enabling/disabling decision is reused when one CU is obtained through different partition path. Specifically, the selected color space for coding the residuals of one CU will be stored when the CU is coded at the first time. Then, when the same CU is obtained by another partition path, instead of checking the RD costs of the two spaces, the stored color space decision will be directly reused.

·     The RD cost of a parent CU is used to decide whether to check the RD cost of the second color space for the current CU. For instance, if the RD cost of the first color space is smaller than that of the second color space for the parent CU, then for the current CU, the second color space is not checked.

·     To reduce the number of tested coding modes, the selected coding mode is shared between two color spaces. Specifically, for intra mode, the preselected intra mode candidates based on SATD-based intra mode selection are shared between two color spaces. For inter and IBC modes, block vector search or motion estimation is performed only once. The block vectors and motion vectors are shared by two color spaces.