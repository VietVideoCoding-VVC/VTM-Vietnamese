## 1.1     Hash based motion estimation for screen content coding 

The VTM reference software uses hash-based motion estimation to handle the sometimes large and irregular motion in screen content. For each reference picture, hash tables corresponding to 4x4 to 64x64 block sizes are generated using a bottom-up approach as follows: 

·     For each 2x2 block, the block hash value is calculated directly from the original sample values (luma samples are used if 4:2:0 chroma format and both luma and chroma sample values are used if 4:4:4 chroma format). The cyclic redundancy check (CRC) value is used as the hash value. 

·     For 4x4, 8x8, 16x16, 32x32 and 64x64 blocks, the hash value of the current block is the CRC value calculated from the CRC values of its four subblocks.

To enable efficient search for matched blocks, the structure of inverted index is used, where hash values are used as to index into a table, and the table entries contain all the blocks with the same hash value as the corresponding table index. The blocks corresponding a given table index are stored as a linked list. Two CRC values, one 16-bit hash and the other 24-bit hash, are calculated for each block. The two hash values are calculated in a similar way but using different CRC truncated polynomials. The first 16-bit CRC value is used as the inverted index. The second 24-bit hash value is stored together with the blocks to resolve hash conflicts in the case more than one matching blocks are found. To reduce the length of the hash table, the hash values of all “simple” blocks (defined as a block with only one sample value in each row or column) are excluded from the hash table.

In motion estimation, if the current block is a square block (except for 128x128 blocks), its hash values are calculated. Then, the encoder queries the corresponding hash table. If hash match is found, the matched block is used as the reference. If the current block is a rectangle block of size NxM (and without loss of generality assume M > N), it will be divided into several non-overlapping square subblocks of size NxN. An example is shown in Figure 59. The encoder will find the first non-simple square subblock and calculate its hash values. Encoder queries the hash values of this NxN square subblock on the hash table corresponding to NxN block size. The one or more matched reference blocks are considered reference block candidates. For each matched reference block candidate, encoder will continue to check whether the hash values of the remaining square subblocks (namely the white region that follows the first non-simple square subblock depicted in Figure 59) are equal to those of the square subblocks adjacent to that reference block candidate. If the hash values of all square subblocks are matched, the reference block candidate will be regarded as a valid reference block.

 

​                               

Figure 59 Motion estimation for rectangular block with hash values for square subblocks.

For inter coding, the hash-based motion search is performed before testing all coding modes. In addition, encoder will reuse the MVs of the hash mode as the starting point candidates in the normal motion estimation process. If the hash-based motion vector exists, which indicates that the block most likely contains screen content, fractional motion estimation is skipped.

To accelerate the encoder, coding modes other than the skip and merge part of ETM_MERGE_SKIP, ETM_AFFINE, and ETM_MERGE_TRIANGLE modes and finer-granularity block splitting are skipped if all of the following conditions are satisfied:

·     Current block size is 64x64, 128x64 or 64x128.

·     An identical reference block is found in a reference picture.

The QP of reference picture is not larger than that of current picture.