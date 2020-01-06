### 1.1.1    Combined inter and intra prediction (CIIP)

In VVC, when a CU is coded in merge mode, if the CU contains at least 64 luma samples (that is, CU width times CU height is equal to or larger than 64), and if both CU width and CU height are less than 128 luma samples, an additional flag is signaled to indicate if the combined inter/intra prediction (CIIP) mode is applied to the current CU. As its name indicates, the CIIP prediction combines an inter prediction signal with an intra prediction signal. The inter prediction signal in the CIIP mode $P_{inter}$ is derived using the same inter prediction process applied to regular merge mode; and the intra prediction signal $P_{intra}$ is derived following the regular intra prediction process with the planar mode. Then, the intra and inter prediction signals are combined using weighted averaging, where the weight value is calculated depending on the coding modes of the top and left neighboring blocks (depicted in Figure 39) as follows: 

* If the top neighbor is available and intra coded, then set isIntraTop to 1, otherwise set isIntraTop to 0;

* If the left neighbor is available and intra coded, then set isIntraLeft to 1, otherwise set isIntraLeft to 0;

* If (isIntraLeft + isIntraLeft) is equal to 2, then $wt$ is set to 3;

* Otherwise, if (isIntraLeft + isIntraLeft) is equal to 1, then $wt$ is set to 2;
* Otherwise, set $wt$ to 1.

The CIIP prediction is formed as follows: 

​                                $ P_{CIIP} = \Big( (4-wt)*P_{inter} + wt * P_{intra} + 2  \Big) \gg 2$                                      (3-36)



**Figure** **40** **– Top and left neighboring blocks used in CIIP weight derivation** 

 