### 1.1.1    Luma mapping with chroma scaling (LMCS)

In VVC, a coding tool called the luma mapping with chroma scaling (LMCS) is added as a new processing block before the loop filters. LMCS has two main components: 1) in-loop mapping of the luma component based on adaptive piecewise linear models; 2) for the chroma components, luma-dependent chroma residual scaling is applied. Figure 52 shows the LMCS architecture from decoder’s perspective. The light-blue shaded blocks in Figure 52 indicate where the processing is applied in the mapped domain; and these include the inverse quantization, inverse transform, luma intra prediction and adding of the luma prediction together with the luma residual. The unshaded blocks in Figure 52 indicate where the processing is applied in the original (i.e., non-mapped) domain; and these include loop filters such as deblocking, ALF, and SAO, motion compensated prediction, chroma intra prediction, adding of the chroma prediction together with the chroma residual, and storage of decoded pictures as reference pictures. The light-yellow shaded blocks in Figure 52 are the new LMCS functional blocks, including forward and inverse mapping of the luma signal and a luma-dependent chroma scaling process. Like most other tools in VVC, LMCS can be enabled/disabled at the sequence level using an SPS flag. 

 

​                               

**Figure** **52** **–** **Luma mapping with chroma scaling architecture** 

#### 1.1.1.1 Luma mapping with piecewise linear model 

The in-loop mapping of the luma component adjusts the dynamic range of the input signal by redistributing the codewords across the dynamic range to improve compression efficiency. Luma mapping makes use of a forward mapping function, *FwdMap*, and a corresponding inverse mapping function, *InvMap*. The *FwdMap* function is signalled using a piecewise linear model with 16 equal pieces. *InvMap* function does not need to be signalled and is instead derived from the *FwdMap* function. 

The luma mapping model is signalled in the adaptation parameter set (APS) syntax structure with aps_params_type set equal to 1 (LMCS_APS). Up to 4 LMCS APS’s can be used in a coded video sequence. Only 1 LMCS APS can be used for a picture. The luma mapping model is signalled using piecewise linear model. The piecewise linear model partitions the input signal’s dynamic range into 16 equal pieces, and for each piece, its linear mapping parameters are expressed using the number of codewords assigned to that piece. Take 10-bit input as an example. Each of the 16 pieces will have 64 codewords assigned to it by default. The signalled number of codewords is used to calculate the scaling factor and adjust the mapping function accordingly for that piece. At the slice level, an LMCS enable flag is signalled to indicate if the LMCS process as depicted in Figure 52 is applied to the current slice. If LMCS is enabled for the current slice, an aps_id is signalled in the slice header to identify the APS that carries the luma mapping parameters.

Each i-th piece, i = 0 … 15, of the *FwdMap* piecewise linear model is defined by two input pivot points InputPivot[] and two output (mapped) pivot points MappedPivot[]. 

The InputPivot[] and MappedPivot[] are computed as follows (assuming 10-bit video):

1)   OrgCW = 64

2)   For i = 0:16, InputPivot[ i ] = i * OrgCW

3)   For i=0:16, MappedPivot[i] is calculated as follows:  
 MappedPivot[ 0 ] = 0;
 for( i = 0; i <16 ; i++)
     MappedPivot[ i + 1 ] = MappedPivot[ i ] + SignalledCW[ i ]



where SignalledCW[ i ] is the signalled number of codewords for the i-th piece. 

As shown in Figure 52, for an inter-coded block, motion compensated prediction is performed in the mapped domain. In other words, after the motion-compensated prediction block   is calculated based on the reference signals in the DPB, the *FwdMap* function is applied to map the luma prediction block in the original domain to the mapped domain,   . For an intra-coded block, the *FwdMap* function is not applied because intra prediction is performed in the mapped domain. After reconstructed block   is calculated, the *InvMap* function is applied to convert the reconstructed luma values in the mapped domain back to the reconstructed luma values in the original domain (  ). The *InvMap* function is applied to both intra- and inter-coded luma blocks.

The luma mapping process (forward and/or inverse mapping) can be implemented using either look-up-tables (LUT) or using on-the-fly computation. If LUT is used, then   and   can be pre-calculated and pre-stored for use at the tile group level, and forward and inverse mapping can be simply implemented as   and   , respectively. Alternatively, on-the-fly computation may be used. Take forward mapping function *FwdMap* as an example. In order to figure out the piece to which a luma sample belongs, the sample value is right shifted by 6 bits (which corresponds to 16 equal pieces). Then, the linear model parameters for that piece are retrieved and applied on-the-fly to compute the mapped luma value. Let i be the piece index, a1, a2 be InputPivot[i] and InputPivot[i+1], respectively, and b1, b2 be MappedPivot[i] and MappedPivot[i+1], respectively. The FwdMap function is evaluated as follows: 

​            (3-69)

The InvMap function can be computed on-the-fly in a similar manner. Generally, the pieces in the mapped domain are not equal sized, therefore the most straightforward inverse mapping process would require comparisons in order to figure out to which piece the current sample value belongs. Such comparisons increase decoder complexity. For this reason, VVC imposes a bistream constraint on the values of the output pivot points MappedPivot[ i ] as follows. Assume the range of the mapped domain (for 10-bit video, this range is [0, 1023]) is divided into 32 equal pieces. If MappedPivot[ i ] is not a multiple of 32, then MappedPivot[ i + 1 ] and MappedPivot[ i ] cannot belong to the same piece of the 32 equal-sized pieces, i.e. MappedPivot[ i + 1 ] >> ( BitDepthY – 5) shall not be equal to MappedPivot[ i ] >> ( BitDepthY – 5). Thanks to such bitstream constraint, the InvMap function can also be carried out using a simple right bit-shift by 5 bits (which corresponds 32 equal-sized pieces) in order to figure out the piece to which the sample value belongs. 

#### 1.1.1.2 Luma-dependent chroma residual scaling 

Chroma residual scaling is designed to compensate for the interaction between the luma signal and its corresponding chroma signals. Whether chroma residual scaling is enabled or not is also signalled at the slice level. If luma mapping is enabled, an additional flag is signalled to indicate if luma-dependent chroma residual scaling is enabled or not. When luma mapping is not used, luma-dependent chroma residual scaling is disabled. Further, luma-dependent chroma residual scaling is always disabled for the chroma blocks whose area is less than or equal to 4.

Chroma residual scaling depends on the average value of top and/or left reconstructed neighbouring luma samples of the current VPDU. If the current CU is inter 128x128, inter 128x64 and inter 64x128, then the chroma residual scaling factor derived for the CU associated with the first VPDU is used for all chroma transform blocks in that CU. Denote   as the average of the reconstructed neighbouring luma samples (see Figure 52). The value of   is computed in the following steps:

1)   Find the index   of the piecewise linear model to which   belongs based on the *InvMap* function.

2)     = cScaleInv[  ], where cScaleInv[] is a 16-piece LUT pre-computed based on the value of SignalledCW[ i ] and a offset value sginalled in APS for chroma residual scaling process.

Unlike luma mapping, which is performed on the sample basis,   is a constant value for the entire chroma block. With   , chroma residual scaling is applied as follows: 

*Encoder side:*  

*Decoder side:*  

#### 1.1.1.3 Encoder-side LMCS parameter estimation



A non-normative reference implementation is provided in the VTM encoder to estimate the LMCS model parameters. Because VTM anchors handle SDR, HDR PQ and HDR HLG differently, the reference algorithm in VTM7.0 is designed differently for SDR, HDR PQ and HDR HLG sequences. For SDR and HDR HLG sequences, the encoder algorithm is based on local luma variance and optimized for PSNR metrics. For HDR PQ sequences, the encoder algorithm is based on luma values and optimized for wPSNR (weighted PSNR) metrics. 

##### 1.1.1.3.1   LMCS parameter estimation for SDR

The basic idea of the VTM7.0 reference implementation for SDR is to assign pieces with more codewords to those dynamic range segments that have lower than average variance, and to assign fewer codewords to those dynamic range segments that have higher than average variance. In this way, smooth areas of the picture will be coded with more codewords than average, and vice versa. For SDR, VTM7.0 provides a reference algorithm and also configurable LMCS parameters for user tuning.

For SDR test sequences, the reference algorithm performs the following signal analysis:

1)   Statistics of the input video are collected and analyzed assuming 10-bit internal coding bit-depth is used. If the internal coding bit-depth is not 10-bit, then bit-depth is first normalized to 10-bit.

2)   Divide the dynamic range of [0, 1023] into 16 equal pieces.

3)   For each luma sample location in the picture, the local spatial variance of luma sample values is calculated using a winSize x winSize (  ) neighbourhood centered on the current position. Denote the specific piece (out of the 32 pieces) to which the current luma sample value belongs as *p*. This local variance is thus associated with the *p*-th piece. 

4)   For each of the 16 pieces, calculate the average local spatial variance (bin_var)

5)   For all valid pieces, an equal number of codewords per piece is allocated;

  , where   is the number of codewords allocated to the *i-*th piece,   is the total number of codewords allowed, and   and   are the index values for the first and last valid piece, respectively.

6)   The allocation of codewords is adjusted such that more codewords are allocated to pieces with lower average local spatial variance and fewer codewords are allocated to pieces with higher average local variance; 

if  

​           (3-70)

else if   ,

​           (3-71)

where   ,   , where   is the average local spatial variance for the luma values in the *i-*th piece;   is the mean of the average local spatial variances across all valid pieces; and   is the percentage of samples in the *i-*th piece over the total number of samples, clipped to the range of [0, 0.4] to avoid aggressive codeword assignment.

7)   If the total number of codewords allocated exceeds the maximum number of allowed codewords. the total number of codewords is reduced by equal amount starting from the first piece.

8)   Adaptation decisions are made to set LMCS slice type, high bit rate, and chroma adjust adaptation parameters based on the relative histogram and average local spatial variances of the luma signal before and after reshaping. Slice type adaptation refers to enabling LMCS for the follow slice type combinations: intra and inter; pictures with temporal-ID 0 only; or inter only. High bit rate adaptation refers to adjusting the number of codewords allocated to pieces for QP values less than or equal to 22. Chroma adjustment adaptation refers to disabling or enabling chroma residue scaling. All these adaptation decisions are based on a series of threshold comparisons.

9)   The SignalledCW [i] values are signalled in an LMCS APS.

 

When LMCS is applied, SSE is used for luma for intra (I) slices and weighted SSE is used for luma for inter (P or B) slices. The weight, w_lmcs(k), is derived as follows based on the codeword assignment of the k-th piece in the piecewise linear model. 

w_lmcs[k] = (SignalledCW[k]/OrgCW)^2                       (3-72)

SSE is always used for chroma mode decision.

In terms of picture-level decision whether to enable LMCS or not, different considerations are given to the different coding configurations. For the Random Access (RA) test conditions, picture analysis is performed for each IRAP picture as explained above. Then, if the average local spatial variance of the original picture is large, or if the average local variance of the mapped picture is large compared to the original, then LMCS is disabled for intra. For the other inter-coded pictures in the same IRAP period, if LMCS is enabled for the IRAP picture,  then LMCS is enabled only for the pictures with temporal layer ID equal to 0. Otherwise, if LMCS is disabled for the IRAP picture, then LMCS is enabled for all the inter-coded pictures. 

For All Intra (AI) and low delay (LD) test conditions, LMCS is enabled for all pictures. For AI, the LMCS parameter estimation is performed for all coded pictures, and the model parameters are updated in LMCS APS for all coded pictures. For LD, the LMCS parameters are estimated at every second interval, and the model parameters are updated in the LMCS APS at the instances of those pictures. 

##### 1.1.1.3.2    LMCS parameter estimation for HDR

In the JVET HDR CTC, two types of HDR sequences are included: PQ and HLG [3]. These two types of sequences are treated differently in the VTM reference encoder. For the PQ sequences, the VTM reference encoder applies luma-based QP adaptation and allows the QP value to vary spatially [3]. For the HLG sequences, static quantization is used [3]. Correspondingly, LMCS is applied differently for these two types of sequences as well. For PQ, LMCS is applied using a default LMCS mapping fucntion calculated as explained below. For HLG, LMCS parameter estimation algorithm similar to that for SDR is applied.

The VTM reference encoder uses wPSNR (weighted PSNR) instead of the conventional PSNR as an objective quality metric in the HDR PQ CTC [3]. The default HDR PQ LMCS curve is calculated to match the dQP function to maximize the wPSNR metric. 

The luma-based QP adaptation derives a local delta QP (dQP) value per CTU based on the average of luma sample values:

dQP(Y) = max(-3, min(6, 0.015*Y - 1.5 – 6 ) )                    (3-73)

where Y is the average luma value,   , maxY=1023 for 10-bit video [8]. The weight (W_SSE) used in wPSNR calculation is derived based on dQP values: 

W_SSE(Y) = 2^(dQP(Y)/3)                                 (3-74)

The default LMCS curve is calculated based on luma sample value as follows:

1)   Compute the slope of the reshaping curve: slope[Y] = sqrt(W_SSE(Y)) = 2^(dQP(Y)/6).  

2)  If signal is in narrow range (also called a standard range) [8], set slope[Y] = 0 for   , or   .

3)   Calculate F[Y] by integrating slope[Y], F[Y+1] = F[Y] + slope[Y], Y =0…maxY-1

4)   *FwdLUT*[Y] is calculated by normalizing F[Y] to [0 maxY], *FwdLUT*[Y] = clip3(0, maxY, round(F[Y]*maxY/F[maxY]))

5)   Calculate the number of codewords for the 16 equal pieces SignalledCW[i], i=0…15, as follows; 

SignalledCW[15] = *FwdLUT*[1023] – *FwdLUT*[960];

for( i = 14; i >=0 ; i – –)

​      SignalledCW[ i ] = FwdLUT[(i + 1) * OrgCW] – FwdLUT[i * OrgCW];



In terms of rate distortion optimized mode decision at the encoder, when LMCS is applied, for an intra (I) slice, SSE is used for luma and weighted SSE is used for chroma as the distortion measure. For an inter (P or B) slice, weighted SSE is used for both luma and chroma. LMCS is applied to all slices.