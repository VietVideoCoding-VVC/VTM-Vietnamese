## 3.6    Entropy coding

In the VVC draft 6, CABAC contains the following major changes compared to the design in HEVC:

–  Core CABAC engine

–  Separate residual coding structure for transform block and transform skip block.

–  Context modeling for transform coefficients

### 3.6.1    Core CABAC engine

The CABAC engine in HEVC uses a table-based probability transition process between 64 different representative probability states. In HEVC, the range ivlCurrRange representing the state of the coding engine is quantized to a set of 4 values prior to the calculation of the new interval range. The HEVC state transition can be implemented using a table containing all 64x4x8-bit pre-computed values to approximate the values of ivlCurrRange * pLPS( pStateIdx ), where pLPS is the probability of the least probable symbol (LPS) and pStateIdx is the index of the current state. Also, a decode decision can be implemented using the pre-computed LUT. First ivlLpsRange is obtained using the LUT as follows. Then, ivlLpsRange is used to update ivlCurrRange and calculate the output binVal. 

ivlLpsRange = rangeTabLps[ pStateIdx ][ qRangeIdx ]                    (3-40)

In VVC draft 6, CABAC has a QP dependent initialization process invoked at the beginning of each slice. Given the initial value of luma QP for the slice, the initial probability state of a context model, denoted as preCtxState, isderived as follows 

m = slopeIdx × 5 – 45                           (3-41)

n = (offsetIdx << 3) +7                     (3-42)

preCtxState = Clip3(1, 127, ((m × (QP – 32)) >> 4) + n)        (3-43)

where slopeIdx and offsetIdx are restricted to 3 bits, and total initialization values are represented by 6-bit precision. The probability state preCtxState represents the probability in the linear domain directly. Hence, preCtxState only needs proper shifting operations before input to arithmetic coding engine, and the logarithmic to linear domain mapping as well as the 256-byte table is saved. 

pStateIdx0 = preCtxState << 3               (3-44)