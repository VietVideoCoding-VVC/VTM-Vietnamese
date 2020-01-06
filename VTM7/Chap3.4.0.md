## 3.4    Inter prediction

For each inter-predicted CU, motion parameters consisting of motion vectors, reference picture indices and reference picture list usage index, and additional information needed for the new coding feature of VVC to be used for inter-predicted sample generation. The motion parameter can be signalled in an explicit or implicit manner. When a CU is coded with skip mode, the CU is associated with one PU and has no significant residual coefficients, no coded motion vector delta or reference picture index. A merge mode is specified whereby the motion parameters for the current CU are obtained from neighbouring CUs, including spatial and temporal candidates, and additional schedules introduced in VVC. The merge mode can be applied to any inter-predicted CU, not only for skip mode. The alternative to merge mode is the explicit transmission of motion parameters, where motion vector, corresponding reference picture index for each reference picture list and reference picture list usage flag and other needed information are signalled explicitly per each CU.

Beyond the inter coding features in HEVC, VVC includes a number of new and refined inter prediction coding tools listed as follows: 

–  Extended merge prediction

–  Merge mode with MVD (MMVD)

–  AMVP mode with symmetric MVD signalling

–  Affine motion compensated prediction

–  Subblock-based temporal motion vector prediction (SbTMVP)

–  Adaptive motion vector resolution (AMVR) 

–  Motion field storage: $1/16$th luma sample MV storage and $8 \times 8$ motion field compression

–  Bi-prediction with CU-level weight (BCW)

–  Bi-directional optical flow (BDOF) 

–  Decoder side motion vector refinement (DMVR)

–  Triangle partition prediction

–  Combined inter and intra prediction (CIIP)

The following text provides the details on the inter prediction methods specified in VVC.