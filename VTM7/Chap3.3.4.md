### 3.3.4    Multiple reference line (MRL) intra prediction

Multiple reference line (MRL) intra prediction uses more reference lines for intra prediction. In Figure 18, an example of 4 reference lines is depicted, where the samples of segments A and F are not fetched from reconstructed neighbouring samples but padded with the closest samples from Segment B and E, respectively. HEVC intra-picture prediction uses the nearest reference line (i.e., reference line 0). In MRL, 2 additional lines (reference line 1 and reference line 3) are used. 

 

The index of selected reference line (`mrl_idx`) is signalled and used to generate intra predictor. For reference line `idx`, which is greater than 0, only include additional reference line modes in MPM list and only signal mpm index without remaining mode. The reference line index is signalled before intra prediction modes, and Planar and DC modes are excluded from intra prediction modes in case a nonzero reference line index is signalled.

​                                ![Fig18](\imgs\Fig18.PNG)

**Figure** **18** **Example of four reference lines neighboring to a prediction block**

 

MRL is disabled for the first line of blocks inside a CTU to prevent using extended reference samples outside the current CTU line. Also, PDPC is disabled when additional line is used. For MRL mode, the derivation of DC value in DC intra prediction mode for non-zero reference line indices is aligned with that of reference line index 0.