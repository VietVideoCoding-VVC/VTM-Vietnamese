### 3.3.3    Position dependent intra prediction combination

In VVC, the results of intra prediction of planar mode are further modified by a position dependent intra prediction combination (PDPC) method. PDPC is an intra prediction method which invokes a combination of the un-filtered boundary reference samples and HEVC style intra prediction with filtered boundary reference samples. PDPC is applied to the following intra modes without signalling: planar, DC, horizontal, vertical, bottom-left angular mode and its *eight* adjacent angular modes, and top-right angular mode and its *eight* adjacent angular modes. 

The prediction sample *pred*(*x*,*y*) is predicted using an intra prediction mode (DC, planar, angular) and a linear combination of reference samples according to the Equation 3-8 as follows:

$pred(x,y)=(wL×R_{-1,y} + wT×R_{x,-1} – wTL ×R_{-1,-1}+(64 – wL – wT+wTL)×pred(x,y) + 32 )>>6$ (3-8)

where $R_{x,-1}, R_{-1,y}$ represent the reference samples located at the top and left of current sample $(x, y)$, respectively, and $R_{-1,-1}$ represents the reference sample located at the top-left corner of the current block.

If PDPC is applied to DC, planar, horizontal, and vertical intra modes, additional boundary filters are not needed, as required in the case of HEVC DC mode boundary filter or horizontal/vertical mode edge filters. PDPC process for DC and Planar modes is identical and clipping operation is avoided. For angular modes, pdpc scale factor is adjusted such that range check is not needed and condition on angle to enable pdpc is removed (scale >=0 is used). In addition, PDPC weight is based on 32 in all angular mode cases.

Figure 17 illustrates the definition of reference samples $(R_{x,-1}, R_{-1,y},$ and $R_{-1,-1})$ for PDPC applied over various prediction modes. The prediction sample $pred (x’, y’)$ is located at $(x’, y’)$ within the prediction block. As an example, the coordinate *x* of the reference sample $R_{x,-1}$ is given by: $x = x’ + y’ + 1$, and the coordinate *y* of the reference sample $R_{-1,y}$ is similarly given by: $y = x’ + y’ + 1$ for the diagonal modes. For the other annular mode, the reference samples $R_{x,-1}$ and $R_{-1,y}$ could be located in fractional sample position. In this case, the sample value of the nearest integer sample location is used.

 

| ![Fig17-A](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig17-A.png)   (a)    Diagonal  top-right mode | (b)    Di![Fig17-B](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig17-B.png)agonal  bottom-left mode |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![Fig17-C](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig17-C.png)  (c) Adjacent diagonal top-right mode | ![Fig17-D](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig17-D.png)         (d)   Adjacent  diagonal bottom-left mode |

**Figure** **17** **-** **Definition of samples used by PDPC applied to diagonal and adjacent angular intra modes.**

The PDPC weights are dependent on prediction modes and are shown in 4. 

**Table** **3‑5 **- Example of PDPC weights according to prediction modes**

| Prediction modes              | wT                                | wL                                | wTL  |
| ----------------------------- | --------------------------------- | --------------------------------- | ---- |
| Diagonal top-right            | 16 >> ( ( *y’*<<1  ) >> *shift*)  | 16 >> ( ( *x’*<<1  ) >> *shift*)  | 0    |
| Diagonal bottom-left          | 16 >> ( ( *y’*<<1  ) >> *shift* ) | 16 >> ( ( *x’*<<1  ) >> *shift* ) | 0    |
| Adjacent diagonal top-right   | 32 >> ( ( *y’*<<1  ) >> *shift* ) | 0                                 | 0    |
| Adjacent diagonal bottom-left | 0                                 | 32 >> ( ( *x’*<<1  ) >> *shift* ) | 0    |