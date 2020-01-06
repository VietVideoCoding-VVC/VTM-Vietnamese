### 3.4.7    Motion field storage 

In VVC, the highest precision of explicitly signalled motion vectors is quarter-luma-sample. In some inter prediction modes such as the affine mode, motion vectors are derived at 1/16th-luma-sample precision and motion compensated prediction is performed at $1/16$th-sample-precision. In terms of internal motion field storage, all motion vectors are stored at $1/16$th-luma-sample precision. 

For temporal motion field storage used by TMVP and ATVMP, motion field compression is performed at $8 \times 8$ granularity in contrast to the $16 \times 16$ granularity in HEVC. 