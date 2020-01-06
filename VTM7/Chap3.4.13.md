### 3.4.13    Miscellaneous inter prediction aspects

To reduce memory bandwidth, the inter-coded $4 \times 4$ size CU is not allowed in VVC. For inter-coded $4 \times 8/8 \times 4$ CU, only uni-directional mode is allowed. When the motion information from merge mode is bi-directional, it is converted to uni-directional by keeping only the list 0 motion information.