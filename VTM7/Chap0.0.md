Document type: 	Approved WG 11 document
	
Title: 	Test Model 7 of Versatile Video Coding (VTM 7)
	
Status:	Approved
	
Date of document:	2019-10-11
	
Source: 	WG 11 (via JVET)
	
Expected action:	Study
	
No. of pages: 	89
	
Email of convenor: 	leonardo@chiariglione.org 

	
Committee URL: 	https://isotc.iso.org/livelink/livelink/open/jtc1sc29wg11


Joint Video Experts Team (JVET)
of ITU-T SG 16 WP 3 and ISO/IEC JTC 1/SC 29/WG 11
16th Meeting: Geneva, CH, 1–11 Oct. 2019	Document: JVET-P2002-v1

Title:	Algorithm description for Versatile Video Coding and Test Model 7 (VTM 7)
Status:	Output document of JVET
Purpose:	Algorithm description for Versatile Video Coding and Test Model 7
Author(s) or
Contact(s):	Jianle Chen
Yan Ye
Seung Hwan Kim
	Email:	jianle.chen@futurewei.com
Yan.Ye@alibaba-inc.com
seunghwan3.kim@lge.com
Source:	Editors
_____________________________
Abstract
The JVET established the Versatile Video Coding (VVC) working draft 7 and the VVC Test Model 7 (VTM7) algorithm description and encoding method at its 16th meeting (1–11 October, Geneva, CH). This document serves as a source of general tutorial information on the VVC design and also provides an encoder-side description of VTM7. The VVC has been developed by a joint collaborative team of ITU-T and ISO/IEC experts known as the Joint Video Experts Team (JVET), which is a partnership of ITU-T Study Group 16 Question 6 (known as VCEG) and ISO/IEC JTC 1/SC 29/WG 11 (known as MPEG). This draft new standard has been designed with two primary goals. The first of these is to specify a video coding technology with a compression capability that is substantially beyond that of the prior generations of such standards, and the second is for this technology to be highly versatile for effective use in a broadened range of applications. Some key application areas for the use of this standard particularly include ultra-high-definition video (e.g., with 3840×2160 or 7620×4320 picture resolution and bit depth of 10 or 12 bits as specified in Rec. ITU-R BT.2100), video with a high dynamic range and wide colour gamut (e.g., with the perceptual quantization or hybrid log-gamma transfer characteristics specified in Rec. ITU-R BT.2100), and video for immersive media applications such as 360° omnidirectional video projected using a common projection format such as the equirectangular or cubemap projection format, in addition to the applications that have commonly been addressed by prior video coding standards.


Ed. Notes:

VVC Test Model 7 (VTM7) algorithm description and encoding method
•	Incorporated JVET-O0683: adaptive color transform
•	Incorporated JVET-O1038: ALF boundary padding
•	Incorporated JVET-P0505: Fixing non-linear ALF clipping values for 8-bit video
•	Incorporated JVET-P0254: Fix number of LMCS segments to 32 regardless of bit depth
•	Incorporated JVET-P0371: Signalling of corrective values for chroma residual scaling
•	Remove bricks
•	Add subpicture
•	Incorporated JVET-P0325: Change the checking order of the first two spatial merge candidates
•	Incorporated JVET-P0057: 1/32-pel precision of PROF motion refinement
•	Incorporated JVET-P1023: Reference picture conditions in DMVR and BDOF

VVC Test Model 6 (VTM6) algorithm description and encoding method
•	Editorial improvements in the section on screen content coding tools 
•	Incorporated JVET-O1124: CCLM restrictions for dualtree to reduce latency
•	Incorporated JVET-O0050: Small chroma block size restrictions for shared tree
•	Incorporated JVET-O0640: Restriction on small chroma blocks
•	Incorporated JVET-O0106: ISP restriction on prediction block size
•	Incorporated JVET-O0277: Restriction on small block sizes
•	Incorporated JVET-O0364: Intra prediction simplifications 
•	Incorporated JVET-O0426: MRL reference samples for DC mode
•	Incorporated JVET-O0502: 67 modes for ISP
•	Incorporated JVET-O0655: Wide-angle in chroma intra angle mapping table for 4:2:2
•	Incorporated JVET-O1153: Intra chroma mode coding cleanup
•	Incorporated JVET-O0925: MIP 8-bit coefficient and simplifications
•	Incorporated JVET-O0315: Intra prediction mode alignment for BDPCM
•	Incorporated JVET-O1136: Unified TS and BDPCM signalling
•	Incorporated JVET-O0258: Disabling IBC for chroma in case of dual tree
•	Incorporated JVET-O0455: Number of IBC merge candidates independent for P/B slices
•	Incorporated JVET-O1170: Bitstream conformance with a virtual IBC buffer concept 
•	Incorporated JVET-O0650: Signalling of chroma QP tables
•	Incorporated JVET-O1168: CU level chroma QP control
•	Incorporated JVET-O0428: LMCS related clean-ups
•	Incorporated JVET-O0272: simplified inverse luma mapping
•	Incorporated JVET-O1109: Unification of chroma residual scaling
•	Incorporated JVET-O0432: LMCS encoder improvement
•	Incorporated JVET-O0057: Half pel AMVR extension with alternative IF
•	Incorporated JVET-O0070: Prediction refinement with optical flow for affine mode
•	Incorporated JVET-O0119: Palette mode coding
•	Incorporated JVET-O0304: unified gradient calculation for BDOF
•	Incorporated JVET-O0055: BDOF subblock early termination threshold
•	Incorporated JVET-O0108: Disabling DMVR and BDOF for CIIP
•	Incorporated JVET-O0634: Unify allowed DMVR and BDOF block sizes
•	Incorporated JVET-O0681: Disabling DMVR, BDOF and BCW for CIIP
•	Incorporated JVET-O0366: BCW index for constructed affine merge candidate
•	Incorporated JVET-O0590: Modified SAD for the center coordinate of DMVR search
•	Incorporated JVET-O0265: Simplified MV storage for TPM
•	Incorporated JVET-O0414: No SMVD for Long term reference picture
•	Incorporated JVET-O0304: Multiplication reduction in BDOF
•	Incorporated JVET-O0090: Alternative chroma filters + CTU chroma filter selection
•	Incorporated JVET-O0662: Modified ALF filtering for Slice, Brick and Virtual boundaries
•	Incorporated JVET- O0625: Apply VB when the bottom CTU boundary is a slice/tile/brick or “360 virtual” boundary
•	Incorporated JVET-O0060: CE5-2.1: Deblocking on 4x4 sample grids
•	Incorporated JVET-O0061: CE5-3.1 Sub-sample MV threshold for deblocking decisions
•	Incorporated JVET-O0159: Non-CE5: Deblocking tC table defined for 10-bit video
•	Incorporated JVET-O0094: Simplification of 48x16 LFNST matrices
•	Incorporated JVET-O0472: LFNST index signalling depends on last position
•	Incorporated JVET-O0368: Disable LFNST for non-DCT2 MTS candidate
•	Incorporated JVET-O0529: Disable LFNST and MIP for implicit MTS
•	Incorporated JVET-O0219: LFNST transform set selection for a CCLM
•	Incorporated JVET-O0213: Limit LFNST up to max TU size
•	Incorporated JVET-O0545: Configurable maximum transform size
•	Incorporated JVET-O0919: QP clipping in scaling process for transform skip
•	Incorporated JVET-O0052: TB-level constraints on context-coded bins
•	Incorporated JVET-O0105: Joint chroma residual coding with multiple modes
•	Incorporated JVET-O0122: Sign context, level mapping, and bitplane coding for TS residual coding
•	Incorporated JVET-O0409: Exclude coded_subblock_flag in TSRC max ctx coded bin count
•	Incorporated JVET-O0617: Context model reduction for sig_coeff_flag
•	Incorporated JVET-O0543: Disallow joint chroma coding for non-I CUs
•	Incorporated JVET-O0065: QP clipping in scaling process for transform skip
•	Incorporated JVET-O0623: Residual coding for transform skip


VVC Test Model 5 (VTM5) algorithm description and encoding method
•	Incorporated JVET-N0866: Unification of implicit transform selection
•	Incorporated JVET-N0193: LFNST (Low-Frequency Non-Separable Transform)
•	Incorporated JVET-N0105: Simplification of LFNST index coding
•	Incorporated JVET-N0217: Matrix weighted intra prediction
•	Incorporated JVET-N0246: Modified dequantization scaling
•	Incorporated JVET-N0847: Support of quantization matrices
•	Incorporated JVET-N0188: Unified rice parameter derivation for coefficient level coding
•	Incorporated JVET-N0194: Context selection of last non-zero coefficient position in reduced TU
•	Incorporated JVET-N0103: Coefficient group size harmonization
•	Incorporated JVET-N0185: Unified MPM list for intra mode coding
•	Incorporated JVET-N0137: Intra chroma partitioning and prediction restriction
•	Incorporated JVET-N0435: Harmonization between WAIP and intra smoothing filters
•	Incorporated JVET-N0308: Restriction of the maximum CU size for ISP to 64×64
•	Incorporated JVET-N0271: CCLM derived with four neighbouring samples
•	Incorporated JVET-N0415: CTU adaptive ALF, and fixed filter set.
•	Incorporated JVET-N0242: Non-Linear Adaptive Loop Filtering (NL-ALF)
•	Incorporated JVET-N0180: ALF line buffer reduction
•	Incorporated JVET-N0473: Deblocking of ISP/SBT TU boundaries
•	Incorporated JVET-N0266: Remove 4x4 unipred, and 4x8/8x4 bipred regular inter modes
•	Incorporated JVET-N0340: Simplified Merge list construction for TPM
•	Incorporated JVET-N0413: quantized residual DPCM 
•	Incorporated JVET-N0054: joint coding of chroma residuals
•	Incorporated JVET-N0251 item 4 on IBC search range.
•	Incorporated JVET-M0253 and JVET-N0247 on hash-based motion estimation.
•	Incorporated JVET-N0280: residual coding for transform skip mode 
•	Incorporated JVET-N0325: using 8-bit fixed precision in BDOF
•	Incorporated JVET-N0146: disable BDOF if BCW or WP is used
•	Incorporated JVET-N0302: CIIP with position-independent weights
•	Incorporated JVET-N0483: disallow the combination of subblock transform with triangle mode
•	Incorporated JVET-N0286: simplified BCW index coding
•	Incorporated JVET-M0140: Subblock transform for inter blocks
•	Incorporated JVET-N0481: BCW index inheritance for constructed affine merge candidate
•	Incorporated JVET-N0407: Disable 8x8/4xN CUs for DMVR
•	Incorporated JVET-N0868: DMVR reconciling with software ticket #214, 25 points SAD full search
•	Incorporated JVET-N0178: Implicitly split BDOF application region along 16x16 boundaries
•	Incorporated JVET-N0146: Align DMVR with BDOF on the conditions
•	Incorporated JVET-N0447/N0400/N0500/N0851, signalling of triangle merge candidate number
•	Incorporated JVET-M0444: Symmetric MVD coding

VVC Test Model 4 (VTM4) algorithm description and encoding method
•	Incorporated JVET-M0118, JVET-M0328 and JVET-M0883: triangle prediction related changes
•	Incorporated JVET-M0487 and JVET-M0063: BDOF related changes
•	Incorporated JVET-M0273: SbTMVP related changes
•	Incorporated JVET-M0111: BCW related changes: 
•	Incorporated JVET-M0453: CABAC core engine
•	Incorporated JVET-M0142: Alternative CCLM downsampling filter
•	Incorporated JVET-M0064: Reduced table size of CCLM parameter derivation
•	Incorporated JVET-M0238: Simplification of PDPC reference samples
•	Incorporated JVET-M0407: IBC reference region modification
•	Incorporated JVET-M0297: 32-length DST-7/DCT-8 using zero-out
•	Incorporated JVET-M0464: Unified MTS and transform skip syntax
•	Incorporated JVET-M0173: rem_abs_gt3_flag in first coding pass
•	Incorporated JVET-M0246: Affine AMVR
•	Incorporated JVET-M0427: luma mapping with chroma scaling (previously known as adaptive in-loop reshaper)
•	Incorporated JVET-M0102: Intra subpartitions (ISP)
•	Incorporated JVET-M0147: Decoder side motion vector refinement
•	Incorporated JVET-M0483: Intra block copy
•	Incorporated JVET-M0102: Intra Subpartitions
•	Incorporated JVET-M 0471: Long tap Deblocking

VVC Test Model 3 (VTM3) algorithm description and encoding method
•	Incorporated Adaptive Loop Filter
o	JVET-L0082: 10 b coeffs (instead of 11)
o	JVET-L0147: Subsampled Laplacian calculation
o	JVET-L0083: Reduction of bits for ALF coefficient fractional part
o	JVET-L0392: minor BF
o	JVET-L0664: Remove the signaling of 5x5 as a special case for luma
•	JVET-L0081: 64x64 luma size virtual pipeline data units (VPDUs)
•	Incorporated Affine related modification, including
o	JVET-L0265: set the chroma subblock size to 4x4 instead of 2x2
o	JVET-L0271: CE4.1.6: Simplification of affine AMVP candidate list construction
o	JVET-L0045: line buffer reduction for affine mode
o	JVET-L0632/L0142: affine merge refinement
o	JVET-L0369/L0055	: moving ATMVP into the affine merge list
•	JVET-L0293: CPR mode for screen content coding
•	JVET-L0646: bi-prediction with weighted averaging
•	JVET-L0256: bi-directional optical flow 
•	JVET-L0231: horizontal wrap-around motion compensation 
•	JVET-L0377: Rounding Align of Adaptive Motion Vector Resolution
•	JVET-L0198/L0468/L0104: fixed subblock size of 8x8 for SbTMVP mode 
•	JVET-L0104: disallow 4x4 bi-prediction
•	Incorporated JVET-L0191: CCLM parameter derivation
•	Incorporated JVET-L0136/JVET-L0085: CCLM with line buffer restriction 
•	Incorporated JVET-L0338/JVET-L0340: Multi-directional LM (MDLM) 
•	Incorporated JVET-L0053/JVET-L0272: chroma DM based on center position
•	Incorporated JVET-L0279: unification of angular intra prediction
•	Incorporated JVET-L0165: intra 6 MPM
•	Incorporated JVET-L0059: simplification on MTS kernel derivation
•	Incorporated JVET-L0111: transform skip condition on transform block size
•	Incorporated JVET-L0285: 8-bit transform matrices
•	Incorporated JVET-L0118: unified MTS signaling
•	Incorporated JVET-L0553: quantization semantics fix
•	Incorporated JVET-L0274: coefficient coding
•	Incorporated JVET-L0628: mode dependent intra smoothing
•	Incorporated JVET-L0283: multiple reference line intra prediction
•	Incorporated JVET-L0414: DF strength dependent on reconstructed luma level
•	Incorporated JVET-L0410: Deblocking tC table
•	JVET_L0124/L0208: triangle partition mode 
•	JVET-L0100: combined intra and inter prediction 
•	Added merge list generation process, including
o	Spatial MVP and Temporal MVP derivation
o	JVET-L0266/: History-based MVP from an FIFO table
o	JVET-L0090: Pairwise average MVP
•	Incorporated JVET-L0054: merge with MVD (MMVD)

VVC Test Model 2 (VTM2) algorithm description and encoding method
•	Incorporated JVET-K0230: Separate trees for intra slices (without multi-DMs) with an implicit split to 64x64; 


•	Incorporated JVET-K0556: Prohibit ternary split of something bigger than 64 in width or height (and not send the bit to indicate ternary type at that level). 
•	Incorporated JVET-K0351 (test c): Keep only the TT restriction (preventing binary split with same orientation in center partition of the ternary split)
•	Incorporated JVET-K0554: Implicit splitting at picture boundaries and ensure MinQTSize at boundary splits
•	Incorporated JVET-K0063: Position dependent intra prediction combination (PDPC)
•	Incorporated JVET-K0190: CCLM only (test 4.1.8)
•	Incorporated JVET-K0122: DC prediction bug fix
•	Incorporated JVET-K0529: 67 modes with 3MPM and FLC for non-MPM
•	Incorporated JVET-K0500: Wide-angle intra prediction for non-square block
•	Incorporated MTS (AMT) modification: Multiple transform selection (MTS)
•	Incorporated subblock TMVP
•	Incorporated adaptive motion vector resolution
•	Incorporated 8x8 and 1/16 pel motion field storage
•	Incorporated affine motion
 
