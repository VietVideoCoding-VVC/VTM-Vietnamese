### 1.1.1    Block differential pulse coded modulation (BDPCM) 

VVC supports block differential pulse coded modulation (BBDPCM) for screen content coding. At the sequence level, a BDPCM enable flag is signalled in the SPS; this flag is signalled only if the transform skip mode (described in the next section) is enabled in the SPS.

When BDPCM is enabled, a flag is transmitted at the CU level if the CU size is smaller than or equal to MaxTsSize by MaxTsSize in terms of luma samples and if the CU is intra coded, where MaxTsSize is the maximum block size for which the transform skip mode is allowed. This flag indicates whether regular intra coding or BDPCM is used. If BDPCM is used, a BDPCM prediction direction flag is transmitted to indicate whether the prediction is horizontal or vertical. Then, the block is predicted using the regular horizontal or vertical intra prediction process with unfiltered reference samples. The residual is quantized and the difference between each quantized residual and its predictor, i.e. the previously coded residual of the horizontal or vertical (depending on the BDPCM prediction direction) neighbouring position, is coded. 

For a block of size M (height) × N (width), let                                 be the prediction residual. Let   denote the quantized version of the residual   . BDPCM is applied to the quantized residual values, resulting in a modified M × N array   with elements   , where   is predicted from its neighboring quantized residual value. For vertical BDPCM prediction mode, for   , the following is used to derive   : 

​                    (3-75)

For horizontal BDPCM prediction mode, for   , the following is used to derive   : 

​                    (3-76)

At the decoder side, the above process is reversed to compute   , as follows:

  if vertical BDPCM is used                    (3-77)

  if horizontal BDPCM is used                  (3-78)

The inverse quantized residuals,   , are added to the intra block prediction values to produce the reconstructed sample values.

The predicted quantized residual values   are sent to the decoder using the same residual coding process as that in transform skip mode residual coding. In terms of the MPM mode for future intra mode coding, horizontal or vertical prediction mode is stored for a BDPCM-coded CU if the BDPCM prediction direction is horizontal or vertical, respectively. For deblocking, if both blocks on the sides of a block boundary are coded using BDPCM, then that particular block boundary is not deblocked.