### 1.1.1    Context modeling for coefficient coding

The selection of probability models for the syntax elements related to absolute values of transform coefficient levels depends on the values of the absolute levels or partially reconstructed absolute levels in a local neighbourhood. The template used is illustrated in Figure 46.

 

​                               

Figure 46: Illustration of the template used for selecting probability models. The black square specifies the current scan position and the blue squares represent the local neighbourhood used.

The selected probability models depend on the sum of the absolute levels (or partially reconstructed absolute levels) in a local neighbourhood and the number of absolute levels greater than 0 (given by the number of sig_coeff_flags equal to 1) in the local neighbourhood. The context modelling and binarization depends on the following measures for the local neighbourhood:

·     numSig: the number of non-zero levels in the local neighbourhood;

·     sumAbs1: the sum of partially reconstructed absolute levels (absLevel1) after the first pass in the local neighbourhood;

·     sumAbs: the sum of reconstructed absolute levels in the local neighbourhood

·     diagonal position (d): the sum of the horizontal and vertical coordinates of a current scan position inside the transform block 

Based on the values of numSig, sumAbs1, and d, the probability models for coding sig_flag, par_flag, gt1_flag, and gt2_flag are selected. The Rice parameter for binarizing abs_remainder is selected based on the values of sumAbs and numSig.

In  Draft 6 reduced 32-point MTS (RMTS32) based on skipping high frequency coefficients is used to reduce computational complexity of 32-point DST-7/DCT-8. And, it accompanies coefficient coding changes considering all types of zero-out (i.e., RMTS32 and the existing zero out for high frequency components in DCT2). Specifically, binarization of last non-zero coefficient position coding is coded based on reduced TU size, and the context model selection for the last non-zero coefficient position coding is determined by the original TU size. In addition, 60 context models are used to encode the sig_coeff_flag of transform coefficients. The selection of context model index is based on a sum of a maximum of five previously partially reconstructed absolute level called locSumAbsPass1 as follows 

–     If cIdx is equal to 0, ctxInc is derived as follows:

ctxInc = 12 * Max( 0, QState – 1 ) +                                       
 Min( ( locSumAbsPass1 + 1 ) >> 1, 3 ) + ( d < 2 ? 8 : ( d < 5 ? 4 : 0 ) )     (3-47)

–     Otherwise (cIdx is greater than 0), ctxInc is derived as follows:

ctxInc = 36 + 8 * Max( 0, QState − 1) +                                
 Min( ( locSumAbsPass1 + 1 ) >> 1, 3 ) + ( d < 2 ? 4 : 0 )                   (3-48)

 