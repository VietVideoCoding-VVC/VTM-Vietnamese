### 3.2.6    Virtual pipeline data units (VPDUs)

Virtual pipeline data units (VPDUs) are defined as non-overlapping units in a picture. In hardware decoders, successive VPDUs are processed by multiple pipeline stages at the same time. The VPDU size is roughly proportional to the buffer size in most pipeline stages, so it is important to keep the VPDU size small. In most hardware decoders, the VPDU size can be set to maximum transform block (TB) size. However, in VVC, ternary tree (TT) and binary tree (BT) partition may lead to the increasing of VPDUs size.

In order to keep the VPDU size as 64x64 luma samples, the following normative partition restrictions (with syntax signaling modification) are applied in VTM, as shown in Figure 12:

–  TT split is not allowed for a CU with either width or height, or both width and height equal to 128.

–  For a 128xN CU with N ≤ 64 (i.e. width equal to 128 and height smaller than 128), horizontal BT is not allowed.

–  For an Nx128 CU with N ≤ 64 (i.e. height equal to 128 and width smaller than 128), vertical BT is not allowed.

  ![Fig12](C:\Users\Thuong\Documents\GitHub\VTM7-Vn\VTM7\imgs\Fig12.png)

**Figure** **12** **– Examples of** **disallowed TT and BT partitioning in VTM**


