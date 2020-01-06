## 3.2    Partitioning

### 3.2.1    Partitioning of the picture into CTUs

Pictures are divided into a sequence of coding tree units (CTUs). The CTU concept is same to that of the HEVC [6][7]. For a picture that has three sample arrays, a CTU consists of an N×N block of luma samples together with two corresponding blocks of chroma samples.Figure 2 shows the example of a picture divided into CTUs.

The maximum allowed size of the luma block in a CTU is specified to be 128×128 (although the maximum size of the luma transform blocks is 64×64).

​                                ![Fig02](imgs\Fig02.png)

Figure 2 – Example of a picture divided into CTUs

