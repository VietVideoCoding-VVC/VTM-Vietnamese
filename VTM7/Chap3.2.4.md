### 3.2.4    CU splits on picture boundaries

As done in HEVC, when a portion of a tree node block exceeds the bottom or right picture boundary, the tree node block is forced to be split until the all samples of every coded CU are located inside the picture boundaries. The following splitting rules are applied in the VTM7:

–  If a portion of a tree node block exceeds both the bottom and the right picture boundaries,

–    If the block is a QT node and the size of the block is larger than the minimum QT size, the block is forced to be split with QT split mode.

–    Otherwise, the block is forced to be split with SPLIT_BT_HOR mode

–  Otherwise if a portion of a tree node block exceeds the bottom picture boundaries,

–    If the block is a QT node, and the size of the block is larger than the minimum QT size, and the size of the block is larger than the maximum BT size, the block is forced to be split with QT split mode.

–    Otherwise, if the block is a QT node, and the size of the block is larger than the minimum QT size and the size of the block is smaller than or equal to the maximum BT size, the block is forced to be split with QT split mode or SPLIT_BT_HOR mode.

–    Otherwise (the block is a BTT node or the size of the block is smaller than or equal to the minimum QT size), the block is forced to be split with SPLIT_BT_HOR mode.

–  Otherwise if a portion of a tree node block exceeds the right picture boundaries,

–    If the block is a QT node, and the size of the block is larger than the minimum QT size, and the size of the block is larger than the maximum BT size, the block is forced to be split with QT split mode.

–    Otherwise, if the block is a QT node, and the size of the block is larger than the minimum QT size and the size of the block is smaller than or equal to the maximum BT size, the block is forced to be split with QT split mode or SPLIT_BT_VER mode.

–    Otherwise (the block is a BTT node or the size of the block is smaller than or equal to the minimum QT size), the block is forced to be split with SPLIT_BT_VER mode.

