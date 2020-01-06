### 3.3.6    Matrix weighted Intra Prediction (MIP)

Matrix weighted intra prediction (MIP) method is a newly added intra prediction technique into VVC. For predicting the samples of a rectangular block of width  $W$ and height $H$, matrix weighted intra prediction (MIP) takes one line of H reconstructed neighbouring boundary samples left of the block and one line of   reconstructed neighbouring boundary samples above the block as input. If the reconstructed samples are unavailable, they are generated as it is done in the conventional intra prediction. The generation of the prediction signal is based on the following three steps, which are averaging, matrix vector multiplication and linear interpolation as shown in Figure 16.

  ![Fig20](imgs\Fig20.png)

Figure 20 – Matrix weighted intra prediction process 



**3.3.6.1 Averaging neighboring samples**

Out of the boundary samples, four samples in the case of   and eight samples in all other cases are extracted by averaging. Specifically, the input boundaries   and   are reduced to smaller boundaries   and   by averaging neighboring boundary samples according to predefined rule depends on block size. Then, the two reduced boundaries   and   are concatenated to a reduced boundary vector   which is thus of size four for blocks of shape    and of size eight for blocks of all other shapes. If   refers to the MIP-mode, this concatenation is defined as follows:      

​     $    bdry_{red}=\left\{
​                \begin{array}{ll}
​                  [bdry_{red}^{top}, bdry_{red}^{left}] \space \space \text{ for $W=H=4$ and $mode<18$ } \\
​                  [bdry_{red}^{left}, bdry_{red}^{top}] \space \space \text{ for $W=H=4$ and $mode\geq 18$ }\\
​                  [bdry_{red}^{top}, bdry_{red}^{left}] \space \space \text{ for max$(W,H)=8$ and $mode<10$ }\\
​                  [bdry_{red}^{left}, bdry_{red}^{top}] \space \space \text{ for max$(W,H)=8$ and $mode\geq 10$ }\\
​                  [bdry_{red}^{top}, bdry_{red}^{left}] \space \space \text{ for max$(W,H)>8$ and $mode<6$ }\\
​                  [bdry_{red}^{left}, bdry_{red}^{top}] \space \space \text{ for max$(W,H)>8$ and $mode\geq 6$ }
​                \end{array}
​              \right.
  $                                         (3-9)



**3.3.6.2 Matrix Multiplication**

A matrix vector multiplication, followed by addition of an offset, is carried out with the averaged samples as an input. The result is a reduced prediction signal on a subsampled set of samples in the original block. Out of the reduced input vector $bdry_{red}$ a reduced prediction signal $pred_{red}$  which is a signal on the downsampled block of width $W_{red } $  and height $H_{red}$  is generated. Here, $W_{red}$  and $H_{red}$  are defined as:

​                      $
​    W_{red}=\left\{
​                \begin{array}{ll}
​                  4 \space \space \space \space\space\space\space\space\space\space\space\space\space\space\space\space\space \space \text{ for max$(W,H) \leq 8$} \\
​                  \text{min}(W,8) \space \space \text{ for max$(W, H) \>8$ and $mode\geq 18$ }
​                \end{array}
​              \right.
  $                                        (3-10)

​                       $
​    H_{red}=\left\{
​                \begin{array}{ll}
​                  4 \space \space \space \space\space\space\space\space\space\space\space\space\space\space\space\space\space \space \text{ for max$(W,H) \leq 8$} \\
​                  \text{min}(H,8) \space \space \space \text{ for max$(W, H) \>8$ and $mode\geq 18$ }
​                \end{array}
​              \right.
  $                                        (3-11)

The reduced prediction signal $pred_{red}$ is computed by calculating a matrix vector product and adding an offset:

​                                                                  $pred_{red} = A \cdot bdry_{red} +b$.

Here, $A$  is a matrix that has $W_{red} \cdot H_{red}$  rows and 4 columns if $W=H=4$ and 8 columns in all other cases.  $b$ is a vector of size $W_{red} \cdot H_{red}$ . The matrix $A$ and the offset vector $b$ are taken from one of the sets $S_0.S_1, S_2$ . One defines an index $idx=idx(W, H)$  as follows:

​                               $
​    idx(W, H)=\left\{
​                \begin{array}{ll}
​                  0 \space \space \space \space\space \text{ for $W=H=4$} \\
​                  1  \space\space\space \space \space \text{ for max$(W, H) =8$ }  \\
​                  2  \space\space\space \space \space \text{ for max$(W, H) >8$  } 
​                \end{array}
​              \right.
  $                                                      (3-12)

Here, each coefficient of the matrix $A$ is represented with 8 bit precision. 



**3.3.6.3 Interpolation** 

The prediction signal at the remaining positions is generated from the prediction signal on the subsampled set by linear interpolation which is a single step linear interpolation in each direction. The matrices and offset vectors needed to generate the prediction signal are taken from three sets $S_0,S_1, S_2$  of matrices. The set  $S_0$ consists of 18 matrices  $A_0^i, i \in \{0, \dots, 17\}$ each of which has 16 rows and 4 columns and 18 offset vectors  $b_0^i, i \in \{0, \dots, 17\}$ each of size 16. Matrices and offset vectors of that set are used for blocks of size $4 \times 4$.The set $S_1$  consists of 10 matrices $A_1^i, i \in \{0, \dots, 9\}$, each of which has  16 rows and 8 columns and 10 offset vectors $b_1^i, i \in \{0, \dots, 9\}$  each of size 16. Matrices and offset vectors of that set are used for blocks of sizes $4\times 8$, $8 \times 4$,  and $8 \times 8$. Finally, the set $S_2$ consists of 6 matrices $A_2^i, i \in \{0, \dots, 5\}$, each of which has 64 rows and 8 columns and of 6 offset vectors $b_1^i, i \in \{0, \dots, 5\}$ of size 64. Matrices and offset vectors of that set or parts of these matrices and offset vectors are used for all other block-shapes.



**3.3.6.4  Signaling of MIP mode and harmonization with other coding tools** 

For each Coding Unit (CU) in intra mode, a flag indicating if an MIP mode is to be applied on the corresponding Prediction Unit (PU) or not is sent. If an MIP mode is to be applied, the MIP modes are directly coded using truncated binary code. 

The number of supported MIP modes depends on block size. For example, 35 modes are available for blocks where $\text{max}(W, H) \leq 8 \space \space  \&\& \space \space W*H < 32$ . And 19 and 11 modes are used for $\text{max}(W, H)=8$ and  $\text{max}(W, H) > 8$ , respectively. In addition, two modes share the same matrix and offset vector to reduce the memory requirement as follows:

​                             $    m=\left\{
​                \begin{array}{ll}
​                  mode \space\space\space\space\space\space\space\space\space\space\space\space \space \text{ for $W=H=4$ and $mode<18$ } \\
​                  mode - 17 \space\space\space \space \text{ for $W=H=4$ and $mode\geq 18$ }\\
​                  mode \space\space\space\space\space\space\space\space\space\space\space\space \space \text{ for max$(W,H)=8$ and $mode<10$ }\\
​                  mode - 9 \space\space\space\space\space \space \text{ for max$(W,H)=8$ and $mode\geq 10$ }\\
​                  mode \space\space\space\space\space\space\space\space\space\space\space \space \space \text{ for max$(W,H)>8$ and $mode<6$ }\\
​                  mode - 5 \space\space\space\space\space \space \text{ for max$(W,H)>8$ and $mode\geq 6$ }
​                \end{array}
​              \right.
  $                         (3-13)

MIP coding mode is harmonized with other coding tools by considering following aspects:

\-     LFNST is enabled for MIP on large blocks. Here, the LFNST transforms corresponding to the planar mode are used.

\-     The reference sample derivation for MIP is performed exactly as for the conventional intra prediction modes.

\-     For the upsampling step used in the MIP-prediction, original reference samples are used instead of downsampled ones.

\-     Clipping is performed before upsampling and not after upsampling