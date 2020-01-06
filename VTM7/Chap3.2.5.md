### 3.2.5    Restrictions on redundant CU splits

The quadtree with nested multi-type tree coding block structure provides a highly flexible block partitioning structure. Due to the types of splits supported the multi-type tree, different splitting patterns could potentially result in the same coding block structure. In VVC, some of these redundant splitting patterns are disallowed.

Figure 11 illustrates the redundant splitting patterns of binary tree splits and ternary tree splits. As shown in Figure 11, two levels of consecutive binary splits in one direction could have the same coding block structure as a ternary tree split followed by a binary tree split of the central partition. In this case, the binary tree split (in the given direction) for the central partition of a ternary tree split is prevented by the syntax. This restriction applies for Cus in all pictures.

  ![Fig11](\imgs\Fig11.png)

**Figure** **11–**Redundant splitting patterns of binary tree split and ternary tree split cases**

When the splits are prohibited as described above, signalling of the corresponding syntax elements is modified to account for the prohibited cases. For example, when any case in Figure 11 is identified (i.e. the binary split is prohibited for a CU of a central partition), the syntax element mtt_split_cu_binary_flag which specifies whether the split is a binary split or a ternary split is not signalled and is instead inferred to be equal to 0 by the decoder.

