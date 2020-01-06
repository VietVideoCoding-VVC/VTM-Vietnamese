### 3.4.5    Subblock-based temporal motion vector prediction (SbTMVP) 

VVC supports the subblock-based temporal motion vector prediction (SbTMVP) method. Similar to the temporal motion vector prediction (TMVP) in HEVC, SbTMVP uses the motion field in the collocated picture to improve motion vector prediction and merge mode for CUs in the current picture. The same collocated picture used by TMVP is used for SbTVMP. SbTMVP differs from TMVP in the following two main aspects: 

1. TMVP predicts motion at CU level but SbTMVP predicts motion at sub-CU level; 

2. Whereas TMVP fetches the temporal motion vectors from the collocated block in the collocated picture (the collocated block is the bottom-right or center block relative to the current CU), SbTMVP applies a motion shift before fetching the temporal motion information from the collocated picture, where the motion shift is obtained from the motion vector from one of the spatial neighboring blocks of the current CU. 

The SbTVMP process is illustrated in Figure 34. SbTMVP predicts the motion vectors of the sub-CUs within the current CU in two steps. In the first step, the spatial neighbor $A_1$ in Figure 34 (a) is examined. If $A_1$ has a motion vector that uses the collocated picture as its reference picture, this motion vector is selected to be the motion shift to be applied. If no such motion is identified, then the motion shift is set to $(0, 0)$. 

In the second step, the motion shift identified in Step 1 is applied (i.e. added to the current block’s coordinates) to obtain sub-CU-level motion information (motion vectors and reference indices) from the collocated picture as shown in Figure 34 (b). The example in Figure 34 (b) assumes the motion shift is set to block $A_1$’s motion. Then, for each sub-CU, the motion information of its corresponding block (the smallest motion grid that covers the center sample) in the collocated picture is used to derive the motion information for the sub-CU. After the motion information of the collocated sub-CU is identified, it is converted to the motion vectors and reference indices of the current sub-CU in a similar way as the TMVP process of HEVC, where temporal motion scaling is applied to align the reference pictures of the temporal motion vectors to those of the current CU. 

​                               ![Fig34-A](\imgs\Fig34-A.png)

**(a)**  **Spatial neighboring blocks used by ATVMP**

 ![Fig34-B](\imgs\Fig34-B.png)

**(b)** **Deriving sub-CU motion field by applying a motion shift from spatial neighbor and scaling the motion information from the corresponding collocated sub-CUs** 

**Figure** **34** **– The SbTMVP process in VVC** 



In VVC, a combined subblock based merge list which contains both SbTVMP candidate and affine merge candidates is used for the signalling of subblock based merge mode. The SbTVMP mode is enabled/disabled by a sequence parameter set (SPS) flag. If the SbTMVP mode is enabled, the SbTMVP predictor is added as the first entry of the list of subblock based merge candidates, and followed by the affine merge candidates. The size of subblock based merge list is signalled in SPS and the maximum allowed size of the subblock based merge list is $5$ in VVC. 

The sub-CU size used in SbTMVP is fixed to be $8 \times 8$, and as done for affine merge mode, SbTMVP mode is only applicable to the CU with both width and height are larger than or equal to $8$.

The encoding logic of the additional SbTMVP merge candidate is the same as for the other merge candidates, that is, for each CU in P or B slice, an additional RD check is performed to decide whether to use the SbTMVP candidate.