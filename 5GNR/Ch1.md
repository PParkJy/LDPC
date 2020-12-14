# Introduction
```
Polar codes are a class of capacity-achieving codes introduced by Arıkan [1]. In the past decade, the interest and research effort on polar codes has been constantly rising in academia and industry alike. Within the ongoing 5th generation wireless systems (5G) standardization process of the 3rd generation partnership project (3GPP), polar codes have been adopted as channel coding for uplink and downlink control information for the enhanced mobile broadband (eMBB) communication service. 5G foresees two other frameworks, namely ultra-reliable low-latency communications (URLLC) and massive machine-type communications (mMTC), for which polar codes are among the possible coding schemes.
```
</br>

```
The construction of a polar code involves the identification of channel reliability values associated to each bit to be encoded. This identification can be effectively performed given a code length and a specific signal-to-noise ratio. However, within the 5G framework, various code lengths, rates and channel conditions are foreseen, and having a different reliability vector for each parameter combination is unfeasible. Thus, substantial effort has been put in the design of polar codes that are easy to implement, having low description complexity, while maintaining good error-correction performance over multiple code and channel parameters.
```

```
The majority of available literature does not take into account the specific codes designed for 5G and their encoding process; given their upcoming widespread utilization, the research community would benefit from considering them within error-correction performance evaluations and encoder/decoder designs. Both the encoding and the decoding process can in fact incur substantial speed and complexity overhead, while the performance of decoders is tightly bound to the characteristics of the polar code. Works focusing on hardware and software implementations can effectively broaden their audience by including compliance to the 5G standard.
```

```
An industry standard is a document providing specifications for delivering a service agreed upon by a group of competing companies. This agreement allows different manufacturers to create products that are compatible with each other, so that standard details are often the result of a quid pro quo among companies. The outcome of the endless discussions and struggles among different agendas is a patchwork of techniques, whose mixture provides acceptable performance; for this reason, a standard usually represents the state-of-theart of a field more than its pinnacle.
```

```
In this paper, we provide a tutorial for the polar code encoding process foreseen by 5G in [2], from the code concatenation, through interleaving functions, to the polar-code specific subchannel allocation and rate-matching schemes. The purpose of this work is to provide the reader with a straightforward, self-contained guide to the understanding and implementation of 5G-compliant encoding of polar codes. While this work does not claim to substitute the reading of the standard, we aim at assisting the reader in its comprehension, restructuring its presentation and reformulating some of the contents to improve readability.
```

```
The remainder of the paper is organized as follows. Section II introduces the basics on polar codes, while Section III details more advanced design features, along with concepts used in the 5G encoding process, such as interleaving and rate-matching. Section IV details a step-by-step guide to 5G polar code encoding. Decoding considerations are addressed in Section V, while conclusions are drawn in Section VI.
```
