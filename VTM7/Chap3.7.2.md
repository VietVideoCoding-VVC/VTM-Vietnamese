### 1.1.1    Deblocking filter

Deblocking filtering process is similar to those in HEVC. However, the following modifications are added. 

a)     The filter strength of the deblocking filter dependent of the averaged luma level of the reconstructed samples.

b)    Deblocking tC table extension and adaptation to 10-bit video

c)     4x4 grid deblocking for luma

d)    Stronger deblocking filter for luma

e)    Stronger deblocking filter for chroma

f)     Deblocking filter for subblock boundary

g)    Deblocking decision adapted to smaller difference in motion

#### 1.1.1.1    Filter strength dependent on reconstructed average luma level

In HEVC, the filter strength of the deblocking filter is controlled by the variables β and tC which are derived from the averaged quantization parameters qPL. In VVC, deblocking filter controls the strength of the deblocking filter by adding offset to qPL according to the luma level of the reconstructed samples. The reconstructed luma level LL is derived as follow:

LL= ( ( p0,0 + p0,3 + q0,0 + q0,3 ) >> 2 ) / ( 1 << bitDepth )             (3-62)

where, the sample values pi,k and qi,k with i = 0..3 and k = 0 and 3 are derived as shown in Figure 51.

 

​                               

**Figure** **51** **–** **Sample position of p****i,k** **and q****i,k**

 

The variable qPL is derived as follows:

qPL = ( ( QpQ + QpP +1 ) >> 1 ) + qpOffset                (3-63)

where QpQ and QpP denote the quantization parameters of the coding units containing the sample q0,0 and p0,0, respectively. The offset qpOffset dependent on transfer function, the values are signalled in the SPS.  

#### 1.1.1.2    Deblocking tC table extension and adaptation to 10-bit video

In VVC, Maximum QP was changed from 51 to 63, and it is desired to reflect corresponding change to deblocking table, which derive values of deblocking parameters tC based on the block QP. The table was also adapted to 10-bit video instead of 8-bit video as was the case for HEVC. The following is updated tC table to accommodate the extension of the QP range and 10-bit video.

tC = [ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 3,4,4,4,4,5,5,5,5,7,7,8,9,10,10,11,13,14,15,17,19,21,24,25,29,33,36,41,45,51,57,64,71,80,89,100,112,125,141,157,177,198,222,250,280,314,352,395]

#### 1.1.1.3 4x4 deblocking grid for luma

HEVC uses an 8x8 deblocking grid for both luma and chroma. In VVC, deblocking on a 4x4 grid for luma boundaries was introduced to handle blocking artifacts from rectangular transform shapes. Parallel friendly luma deblocking on a 4x4 grid is achieved by restricting the number of samples to be deblocked to 1 sample on each side of a vertical luma boundary where one side has a width of 4 or less or to 1 sample on each side of a horizontal luma boundary where one side has a height of 4 or less.

#### 1.1.1.4    Stronger deblocking filter for luma

A bilinear filter (stronger deblocking filter) is used when samples at either one side of a boundary belong to a large block. A sample belonging to a large block is defined as when the width is larger than or equal to 32 for a vertical edge, and when height is larger than or equal to 32 for a horizontal edge. Block boundary samples pi for i=0 to Sp-1 and qi for j=0 to Sq-1 are then replaced by linear interpolation as follows:

​       (3-64)

​     (3-65)

where   and   term is a position dependent clipping and   ,   ,   ,   and   are given below:

**Table** **3****‑****13** **–** **Derivation of stronger deblocking parameters for luma**

| Sp, Sq  7, 7  (p side: 7,  q side: 7) |      |
| ------------------------------------- | ---- |
| 7, 3   (p side: 7  q side: 3)         |      |
| 3, 7   (p side: 3  q side: 7)         |      |
| 7, 5   (p side: 7  q side: 5)         |      |
| 5, 7   (p side: 5  q side: 7)         |      |
| 5, 5   (p side: 5  q side: 5)         |      |
| 5, 3   (p side: 5  q side: 3)         |      |
| 3, 5   (p side: 3  q side: 5)         |      |

Above mentioned stronger luma filters are used only if all of the **Condition1**, **Condition2** and **Condition 3** are TRUE. The condition 1 is the “large block condition”. This condition detects whether the samples at P-side and Q-side belong to large blocks. The condition 2 and condition 3 are determined by: 

**Condition2** = (d < β) ? TRUE: FALSE

**Condition3 =** StrongFilterCondition = (dpq is less than ( β >> 2 ), sp3 + sq3 is less than ( 3*β >> 5 ), and Abs( p0 − q0 ) is less than ( 5 * tC + 1 ) >> 1) ? TRUE : FALSE

#### 1.1.1.5    Strong deblocking filter for chroma

The following strong deblocking filter for chroma is defined:

p2′= (3*p3+2*p2+p1+p0+q0+4) >> 3                     (3-66)

p1′= (2*p3+p2+2*p1+p0+q0+q1+4) >> 3                  (3-67)

p0′= (p3+p2+p1+2*p0+q0+q1+q2+4) >> 3                  (3-68)

The above chroma filter performs deblocking on a 8x8 chroma sample grid. The chroma strong filters are used on both sides of the block boundary. Here, the chroma filter is selected when both sides of the chroma edge are greater than or equal to 8 (in unit of chroma sample), and the following decision with three conditions are satisfied. The first one is for decision of boundary strength as well as large block. The second and third one are basically the same as for HEVC luma decision, which are on/off decision and strong filter decision, respectively. In the first decision, boundary strength (bS) is modified for chroma filtering as shown in **Table 3‑14**. The conditions in **Table 3‑14** are checked sequentially. If a condition is satisfied then the remaining conditions with lower priorities are skipped.

**Table** **3****‑****14** **–** **The modified boundary strength**

| Priority | Conditions                                                   | Y    | U    | V    |
| -------- | ------------------------------------------------------------ | ---- | ---- | ---- |
| 5        | At  least one of the adjacent blocks is intra                | 2    | 2    | 2    |
| 4        | At  least one of the adjacent blocks has non-zero transform coefficients | 1    | 1    | 1    |
| 3        | Absolute  difference between the motion vectors that belong to the adjacent blocks is  greater than or equal to one half luma sample | 1    | N/A  | N/A  |
| 2        | Motion  prediction in the adjacent blocks refers to vectors is different | 1    | N/A  | N/A  |
| 1        | Otherwise                                                    | 0    | 0    | 0    |

 

Chroma deblocking is performing when bS is equal to 2, or bS is equal to 1 when a large block boundary is detected. The second and third condition is basically the same as HEVC luma strong filter decision.

#### 1.1.1.6    Deblocking filter for subblock boundary

The deblocking filtering process are applied on a 4x4 grid for CU boundaries and transform subblock boundaries and on an 8x8 grid for prediction subblock boundaries. The prediction subblock boundaries include the prediction unit boundaries introduced by STMVP and affine modes, and the transform subblock boundaries include the transform unit boundaries introduced by SBT and ISP modes, and transforms due to implicit split of large CUs. 

For SBT and ISP subblocks, similar to the logic in TU in HEVC deblocking filter, the deblocking filter is applied on TU boundary when there are non-zero coefficients in either transform subblock across the edge. 

For SbTMVP and affine prediction subblocks, similar to the logic in PU in HEVC, the deblocking filter is applied on 8x8 grid with the consideration of the difference between motion vectors and reference pictures of the neighboring prediction subblock.

Transform block boundaries can at most be deblocked with 5 samples on a side of transform boundary which also is part of a coding block where SbTMVP or affine is used to enable parallel friendly deblocking. Internal prediction subblock boundaries 4 samples from a transform block boundary are at most deblocked by 1 sample on each side, internal prediction subblock boundaries 8 samples away from a transform block boundary are at most deblocked by 2 samples on each side of the boundary and other internal prediction subblock boundaries are at most deblocked with 3 samples on each side of the boundary.

#### 1.1.1.7    Deblocking decision adapted to smaller difference in motion

HEVC enables deblocking of a prediction unit boundary when the difference in at least one motion vector component between blocks on respective side of the boundary is equal to or larger than a threshold of 1 sample. In VTM, a threshold of a half luma sample is introduced to also enable removal of blocking artifacts originating from boundaries between inter prediction units that have a small difference in motion vectors.