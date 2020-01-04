### 3.3.6    Matrix weighted Intra Prediction (MIP)

Matrix weighted intra prediction (MIP) method is a newly added intra prediction technique into VVC. For predicting the samples of a rectangular block of width  $W$ and height $H$, matrix weighted intra prediction (MIP) takes one line of H reconstructed neighbouring boundary samples left of the block and one line of   reconstructed neighbouring boundary samples above the block as input. If the reconstructed samples are unavailable, they are generated as it is done in the conventional intra prediction. The generation of the prediction signal is based on the following three steps, which are averaging, matrix vector multiplication and linear interpolation as shown in Figure 16.

  ![Fig20](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig20.png)

Figure 20 – Matrix weighted intra prediction process 

**3.3.6.1 Averaging neighboring samples**

Out of the boundary samples, four samples in the case of   and eight samples in all other cases are extracted by averaging. Specifically, the input boundaries   and   are reduced to smaller boundaries   and   by averaging neighboring boundary samples according to predefined rule depends on block size. Then, the two reduced boundaries   and   are concatenated to a reduced boundary vector   which is thus of size four for blocks of shape    and of size eight for blocks of all other shapes. If   refers to the MIP-mode, this concatenation is defined as follows:

​             (3-9)

 

**3.3.6.2 Matrix Multiplication**

A matrix vector multiplication, followed by addition of an offset, is carried out with the averaged samples as an input. The result is a reduced prediction signal on a subsampled set of samples in the original block. Out of the reduced input vector   a reduced prediction signal   which is a signal on the downsampled block of width   and height   is generated. Here,   and   are defined as:

​                           (3-10)

​                           (3-11)

The reduced prediction signal   is computed by calculating a matrix vector product and adding an offset:

  .

Here,   is a matrix that has   rows and 4 columns if   and 8 columns in all other cases.   is a vector of size   . The matrix   and the offset vector   are taken from one of the sets   ,   ,   One defines an index   as follows:

​                          (3-12)

Here, each coefficient of the matrix A is represented with 8 bit precision. 

**3.3.6.3 Interpolation** 

The prediction signal at the remaining positions is generated from the prediction signal on the subsampled set by linear interpolation which is a single step linear interpolation in each direction. The matrices and offset vectors needed to generate the prediction signal are taken from three sets   ,   of matrices. The set   consists of 18 matrices   each of which has 16 rows and 4 columns and 18 offset vectors   each of size 16. Matrices and offset vectors of that set are used for blocks of size   The set   consists of 10 matrices   , each of which has   rows and 8 columns and 10 offset vectors   each of size 16. Matrices and offset vectors of that set are used for blocks of sizes   ,   and   . Finally, the set   consists of 6 matrices   , each of which has 64 rows and 8 columns and of 6 offset vectors   of size 64. Matrices and offset vectors of that set or parts of these matrices and offset vectors are used for all other block-shapes.

**3.3.6.4  Signaling of MIP mode and harmonization with other coding tools** 

For each Coding Unit (CU) in intra mode, a flag indicating if an MIP mode is to be applied on the corresponding Prediction Unit (PU) or not is sent. If an MIP mode is to be applied, the MIP modes are directly coded using truncated binary code. 

The number of supported MIP modes depends on block size. For example, 35 modes are available for blocks where   . And 19 and 11 modes are used for   and   , respectively. In addition, two modes share the same matrix and offset vector to reduce the memory requirement as follows:

​    (3-13)

MIP coding mode is harmonized with other coding tools by considering following aspects:

\-     LFNST is enabled for MIP on large blocks. Here, the LFNST transforms corresponding to the planar mode are used.

\-     The reference sample derivation for MIP is performed exactly as for the conventional intra prediction modes.

\-     For the upsampling step used in the MIP-prediction, original reference samples are used instead of downsampled ones.

\-     Clipping is performed before upsampling and not after upsampling