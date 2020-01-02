# 2    Scope

The normative decoding process for Versatile Video Coding is specified in the VVC draft 6 text specification document [1]. The VTM7.0 reference software is provided to demonstrate a reference implementation of non-normative encoding techniques and the normative decoding process for VVC. The reference software can be accessed via 

https://vcgit.hhi.fraunhofer.de/jvet/VVCSoftware_VTM.git

This document provides an algorithm description as well as an encoder-side description of the VVC Test Model 5, which serves as a tutorial for the algorithm and encoding model implemented in the VTM7.0 software. The purpose of this document is to share a common understanding of the coding features of VVC and the reference encoding methods supported in the VTM7.0 software, in order to facilitate the assessment of the technical impact of new technologies during the standardization process. Common test conditions and software reference configurations that should be used for experimental work for conventional standard-dynamic range rectangular video content are described in JVET-N1010 [2]. Common test conditions specific to video content with high dynamic range and wide colour gamut are described in JVET-N1011 [3]. Common test conditions specific to video content for 360° omnidirectional video applications are described in JVET-L1012 [4]. When encoding and decoding 360° omnidirectional video, an additional software package called the 360Lib needs to be used together with using the VTM software to process, encode/decode and compute the spherical quality metrics. The 360Lib software is available at: 

https://jvet.hhi.fraunhofer.de/svn/svn_360Lib/ 

Additionally, document JVET-M1004 [5] describes the algorithms used in 360Lib to process, code, and measure quality of 360° omnidirectional video.