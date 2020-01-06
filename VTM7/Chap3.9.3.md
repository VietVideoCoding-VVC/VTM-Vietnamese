### 1.1.1    Residual coding for transform skip mode 

VVC allows the transform skip mode to be used for luma blocks of size up to MaxTsSize by MaxTsSize, where the value of MaxTsSize is signaled in the PPS and can be at most 32. When a CU is coded in transform skip mode, its prediction residual is quantized and coded using the transform skip residual coding process. This process is modified from the transform coefficient coding process described in 3.6.2. In transform skip mode, the residuals of a TU are also coded in units of non-overlapped subblocks of size 4x4. For better coding efficiency, some modifications are made to customize the residual coding process towards the residual signal’s characteristics. The following summarizes the differences between transform skip residual coding and regular transform residual coding:

 

\-     Forward scanning order is applied to scan the subblocks within a transform block and also the positions within a subblock; 

\-     no signalling of the last (x, y) position;

\-     coded_sub_block_flag is coded for every subblock except for the last subblock when all previous flags are equal to 0;

\-     sig_coeff_flag context modelling uses a reduced template, and context model of sig_coeff_flag depends on top and left neighbouring values;

\-     context model of abs_level_gt1 flag also depends on the left and top sig_coeff_flag context modelling uses a reduced template, and context model of sig_coeff_flag depends on top and left neighbouring values;

\-     values.

\-     par_level_flag using only one context model;

\-     additional greater than 3, 5, 7, 9 flags are signalled to indicate the coefficient level, one context for each flag;

\-     modified rice parameter derivation for the binarization of the remainder values;

\-     context model of the sign flag is determined based on left and above neighbouring values and the sign flag is parsed after sig_coeff_flag to keep all context coded bins together;

 

For each subblock, if the coded_subblock_flag is equal to 1 (i.e., there is at least one non-zero quantized residual in the subblock), coding of the quantized residual levels is performed in three scan passes (see Figure 55):

–  **First scan pass**: significance flag (sig_coeff_flag), sign flag (coeff_sign_flag), absolute level greater than 1 flag (abs_level_gtx_flag[0]), and parity (par_level_flag) are coded. For a given scan position, if sig_coeff_flag is equal to 1, then coeff_sign_flag is coded, followed by the abs_level_gtx_flag[0] (which specifies whether the absolute level is greater than 1). If abs_level_gtx_flag[0] is equal to 1, then the par_level_flag is additionally coded to specify the parity of the absolute level. 

–  **Greater than x scan pass**: for each scan position whose absolute level is greater than 1, up to four abs_level_gtx_flag[i] for i = 1...4 are coded to indicate if the absolute level at the given position is greater than 3, 5, 7, or 9, respectively. 

–  **Remainder scan pass**: The remainder of the absolute level are coded for all scan positions with abs_level_gtx_flag[4] equal to 1 (that is, the absolute level is greater than 9). The remainder of the absolute levels are binarized using reduced rice parameter derivation template.

The bins in scan passes #1 and #2 (the first scan pass and the greater than x scan pass) are context coded until the maximum number of context coded bins in the TU have been exhausted. The maximum number of context coded bins in a residual block is limited to 2*block_width*block_height, or equivalently, 2 context coded bins per sample position on average. The bins in the last scan pass (the remainder scan pass) are bypass coded.

​                               

Figure 55 – residual coding passes for transform skip blocks

Further, for a block not coded in the BDPCM mode, a level mapping mechanism is applied to transform skip residual coding. Level mapping uses the top and left neighbouring coefficient levels to predict the current coefficient level in order to reduce signalling cost. For a given residual position, denote *absCoeff* as the absolute coefficient level before mapping and *absCoeffMod* as the coefficient level after mapping. Let X0 denote the absolute coefficient level of the left neighbouring position and let X1 denote the absolute coefficient level of the above neighbouring position. The level mapping is performed as follows:

 

​      pred = max(X0, X1);

​      if (absCoeff = = pred)

​            absCoeffMod = 1;

​      else

​            absCoeffMod = (absCoeff < pred) ? absCoeff + 1 : absCoeff;

Then, the absCoeffMod value is coded as described above.