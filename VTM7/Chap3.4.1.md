### 3.4.1    Extended merge prediction

In VVC, the merge candidate list is constructed by including the following five types of candidates in order:

1. Spatial MVP from spatial neighbour CUs

2. Temporal MVP from collocated CUs

3. History-based MVP from an FIFO table

4. Pairwise average MVP

5. Zero MVs.

The size of merge list is signalled in slice header and the maximum allowed size of merge list is 6. For each CU code in merge mode, an index of best merge candidate is encoded using truncated unary binarization (TU). The first bin of the merge index is coded with context and bypass coding is used for other bins.

The generation process of each category of merge candidates is provided in this session.

#### 3.4.1.1   Spatial candidates derivation

The derivation of spatial merge candidates in VVC is same to that in HEVC except the positions of first two merge candidates are swapped. A maximum of four merge candidates are selected among candidates located in the positions depicted in Figure 21. The order of derivation is $B_0, A_0, B_1, A_1$ and $B_2$. Position $B_2$ is considered only when one or more than one CUs of position $B_0, A_0, B_1, A_1$ are not available (e.g. because it belongs to another slice or tile) or is intra coded. After candidate at position $A_1$ is added, the addition of the remaining candidates is subject to a redundancy check which ensures that candidates with same motion information are excluded from the list so that coding efficiency is improved. To reduce computational complexity, not all possible candidate pairs are considered in the mentioned redundancy check. Instead only the pairs linked with an arrow in Figure 22 are considered and a candidate is only added to the list if the corresponding candidate used for redundancy check has not the same motion information.

 

​                                ![Fig21](imgs\Fig21.PNG)

Figure 21– Positions of spatial merge candidate

 

 ![Fig22](\imgs\Fig22.png)

Figure 22 – Candidate pairs considered for redundancy check of spatial merge candidates

#### 3.4.1.2 Temporal candidates derivation

In this step, only one candidate is added to the list. Particularly, in the derivation of this temporal merge candidate, a scaled motion vector is derived based on co-located CU belonging to the collocated referenncee picture. The reference picture list to be used for derivation of the co-located CU is explicitly signalled in the slice header. The scaled motion vector for temporal merge candidate is obtained as illustrated by the dotted line in Figure 23, which is scaled from the motion vector of the co-located CU using the POC distances, $tb$ and $td$, where $tb$ is defined to be the POC difference between the reference picture of the current picture and the current picture and td is defined to be the POC difference between the reference picture of the co-located picture and the co-located picture. The reference picture index of temporal merge candidate is set equal to zero. 

![Fig23](\imgs\Fig23.PNG)

Figure 23 – Illustration of motion vector scaling for temporal merge candidate

The position for the temporal candidate is selected between candidates $C_0$ and $C_1$, as depicted in Figure 24. If CU at position $C_0$ is not available, is intra coded, or is outside of the current row of CTUs, position $C_1$ is used. Otherwise, position $C_0$ is used in the derivation of the temporal merge candidate.

  ![Fig24](imgs\Fig24.png)

Figure 24 – Candidate positions for temporal merge candidate, C0 and C1

#### 3.4.1.3 History-based merge candidates derivation 

The history-based MVP (HMVP) merge candidates are added to merge list after the spatial MVP and TMVP. In this method, the motion information of a previously coded block is stored in a table and used as MVP for the current CU. The table with multiple HMVP candidates is maintained during the encoding/decoding process. The table is reset (emptied) when a new CTU row is encountered. Whenever there is a non-subblock inter-coded CU, the associated motion information is added to the last entry of the table as a new HMVP candidate.

The HMVP table size *S* is set to be 6, which indicates up to 6 History-based MVP (HMVP) candidates may be added to the table. When inserting a new motion candidate to the table, a constrained first-in-first-out (FIFO) rule is utilized wherein redundancy check is firstly applied to find whether there is an identical HMVP in the table. If found, the identical HMVP is removed from the table and all the HMVP candidates afterwards are moved forward,

HMVP candidates could be used in the merge candidate list construction process. The latest several HMVP candidates in the table are checked in order and inserted to the candidate list after the TMVP candidate. Redundancy check is applied on the HMVP candidates to the spatial or temporal merge candidate. 

To reduce the number of redundancy check operations, the following simplifications are introduced:

1)   Number of HMPV candidates is used for merge list generation is set as $(N <= 4 ) \space ? \space M: (8 – N)$, wherein $N$ indicates number of existing candidates in the merge list and $M$ indicates number of available HMVP candidates in the table.

2)   Once the total number of available merge candidates reaches the maximally allowed merge candidates minus 1, the merge candidate list construction process from HMVP is terminated.

#### 3.4.1.4 Pair-wise average merge candidates derivation 

Pairwise average candidates are generated by averaging predefined pairs of candidates in the existing merge candidate list, and the predefined pairs are defined as $\{(0, 1), (0, 2), (1, 2), (0, 3), (1, 3), (2, 3)\}$, where the numbers denote the merge indices to the merge candidate list. The averaged motion vectors are calculated separately for each reference list. If both motion vectors are available in one list, these two motion vectors are averaged even when they point to different reference pictures; if only one motion vector is available, use the one directly; if no motion vector is available, keep this list invalid.

When the merge list is not full after pair-wise average merge candidates are added, the zero MVPs are inserted in the end until the maximum merge candidate number is encountered.