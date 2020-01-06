### 3.4.2    Merge mode with MVD (MMVD)

In addition to merge mode, where the implicitly derived motion information is directly used for prediction samples generation of the current CU, the merge mode with motion vector differences (MMVD) is introduced in VVC. A MMVD flag is signalled right after sending a skip flag and merge flag to specify whether MMVD mode is used for a CU.

In MMVD, after a merge candidate is selected, it is further refined by the signalled MVDs information. The further information includes a merge candidate flag, an index to specify motion magnitude, and an index for indication of motion direction. In MMVD mode, one for the first two candidates in the merge list is selected to be used as MV basis. The merge candidate flag is signalled to specify which one is used.

​                               ![Fig25](\imgs\Fig25.png)

Figure 25 – MMVD Search Point

Distance index specifies motion magnitude information and indicate the pre-defined offset from the starting point. As shown in Figure 25, an offset is added to either horizontal component or vertical component of starting MV. The relation of distance index and pre-defined offset is specified in Table 3‑8

Table 3‑8 – The relation of distance index and pre-defined offset

| **Distance IDX**                    | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    |
| ----------------------------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| **Offset (in unit of luma sample)** | 1/4  | 1/2  | 1    | 2    | 4    | 8    | 16   | 32   |

Direction index represents the direction of the MVD relative to the starting point. The direction index can represent of the four directions as shown in Table 3‑9. It’s noted that the meaning of MVD sign could be variant according to the information of starting MVs. When the starting MVs is an un-prediction MV or bi-prediction MVs with both lists point to the same side of the current picture (i.e. POCs of two references are both larger than the POC of the current picture, or are both smaller than the POC of the current picture), the sign in Table 3‑9 specifies the sign of MV offset added to the starting MV. When the starting MVs is bi-prediction MVs with the two MVs point to the different sides of the current picture (i.e. the POC of one reference is larger than the POC of the current picture, and the POC of the other reference is smaller than the POC of the current picture), the sign in Table 3‑9 specifies the sign of MV offset added to the list0 MV component of starting MV and the sign for the list1 MV has opposite value.

Table 3‑9 – Sign of MV offset specified by direction index

| **Direction IDX** | 00   | 01   | 10   | 11   |
| ----------------- | ---- | ---- | ---- | ---- |
| **x-axis**        | +    | –    | N/A  | N/A  |
| **y-axis**        | N/A  | N/A  | +    | –    |

 