### 3.2.2    Partitioning of pictures into subpictures, slices, tiles

A picture is divided into one or more tile rows and one or more tile columns. A tile is a sequence of CTUs that covers a rectangular region of a picture.

A slice consists of an integer number of complete tiles or an integer number of consecutive complete CTU rows within a tile of a picture.

Two modes of slices are supported, namely the raster-scan slice mode and the rectangular slice mode. In the raster-scan slice mode, a slice contains a sequence of complete tiles in a tile raster scan of a picture. In the rectangular slice mode, a slice contains either a number of complete tiles that collectively form a rectangular region of the picture or a number of consecutive complete CTU rows of one tile that collectively form a rectangular region of the picture. Tiles within a rectangular slice are scanned in tile raster scan order within the rectangular region corresponding to that slice.

A subpicture contains one or more slices that collectively cover a rectangular region of a picture.

Figure 3 shows an example of raster-scan slice partitioning of a picture, where the picture is divided into 12 tiles and 3 raster-scan slices.

  ![Fig03](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig03.png)

Figure 3 – Example of a picture partitioned into tiles and reaster-scan slices

Figure 4 shows an example of rectangular slice partitioning of a picture, where the picture is divided into 24 tiles (6 tile columns and 4 tile rows) and 9 rectangular slices.

  ![Fig04](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig04.png)

Figure 4 – Example of a picture partitioned into tiles and rectangular slices

Figure 5 shows an example of a picture partitioned into tiles and rectangular slices, where the picture is divided into 4 tiles (2 tile columns and 2 tile rows) and 4 rectangular slices.

  ![Fig05](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig05.png)

Figure 5 – Example of a picture partitioned into 4 tiles and 4 rectangular slices

Figure 6 shows an example of subpicture partitioning of a picture, where a picture is partitioned into 28 subpictures of varying dimensions.

  ![Fig06](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig06.png)

Figure 6 – Example of a picture partitioned into 28 subpictures

