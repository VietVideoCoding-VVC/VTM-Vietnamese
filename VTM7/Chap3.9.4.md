### 1.1.1    Palette mode

In VVC, the palette mode is used for screen content coding in 4:4:4 color format. When palette mode is enabled, a flag is transmitted at the CU level if the CU size is smaller than or equal to 64x64 indicating whether palette mode is used. A palette coded coding unit (CU) is treated as a prediction mode other than intra prediction, inter prediction, and intra block copy (IBC) mode.

If the palette mode is utilized, the sample values in the CU are represented by a small set of representative colour values. The set is referred to as the palette. For pixels with values close to the palette colors, the palette indices are signalled. It is also possible to specify a sample that is outside the palette by signalling an escape symbol followed by quantized component values. This is illustrated in Figure 56.

​                               

Figure 56: Example of a block coded in palette mode

For coding of the palette, a palette predictor is maintained. The predictor is initialized to 0 at the beginning of each slice for non-wavefront case and at the beginning of each CTU row for wavefront case. For each entry in the palette predictor, a reuse flag is signalled to indicate whether it is part of the current palette in the CU. The reuse flags are sent using run-length coding of zeros. After this, the number of new palette entries and the component values for the new palette entries are signalled. After encoding the palette coded CU, the palette predictor will be updated using the current palette, and entries from the previous palette predictor that are not reused in the current palette will be added at the end of the new palette predictor until the maximum size allowed is reached. An escape flag is signaled for each CU to indicate if escape symbols are present in the current CU. If escape symbols are present, the palette table is augmented by one and the last index is assigned to be the escape symbol.

Palette indices of samples in a CU form a palette index map. The index map is coded using horizontal and vertical traverse scans as shown in Figure 57. The scan order is explicitly signalled in the bitstream using the palette_transpose_flag.

 

Figure 57: Horizontal and vertical traverse scans

The palette indices are coded using two main palette sample modes: 'INDEX' and 'COPY_ABOVE'. The mode is signalled using a flag except for the top row when horizontal scan is used, the first column when the vertical scan is used, or when the previous mode was 'COPY_ABOVE'. In the 'COPY_ABOVE' mode, the palette index of the sample in the row above is copied. In the 'INDEX' mode, the palette index is explicitly signalled. For both 'INDEX' and 'COPY_ABOVE' modes, a run value is signalled which specifies the number pixels that are coded using the same mode.

The encoding order for index map is as follows: First, the number of index values associated to ‘INDEX’ runs is signalled. This is followed by signalling of the actual index values associated to ‘INDEX’ runs for the entire CU using truncated binary coding. Then the palette mode (INDEX or COPY_ABOVE) and run length for each run are signalled in an interleaved manner. Finally, the quantized escape mode colors for the entire CU are grouped together and coded with exponential Golomb coding.

For slices with dual luma/chroma tree, palette is applied on luma (Y component) and chroma (Cb and Cr components) separately. For slices of single tree, palette will be applied on Y, Cb, Cr components jointly, i.e., each entry in the palette contains Y, Cb, Cr values.

For deblocking, the palette coded block on the sides of a block boundary is not deblocked. 