# Advanced Design
```
In this section, we introduce more advanced polar codeing principles. 
The discussion is not limited to frozen set calculation and encoding/decoding algorithms including techniques for rate matching and assistant bits design for 5G applications
```
A. Frozen set design    
```
In general, the reliability order of the bit-channels depends on the channel condition and on the code length, and therefore is not universal. 
This non-universality poses huge practical problems in the construction of polar codes when a large range of code lengths and rates is envisaged. 
Many methods to design the frozen sets on-the-fly with limited complexity have been proposed [4].
 Along with Bhattacharyya parameter tracking, Arıkan initially proposed to use Monte-Carlo simulations to estimate bit-channel reliabilities [1]. 
The density evolution (DE) method, initially proposed in [5] and improved in [6], can provide theoretical guarantees on the estimation accuracy, however at a high computational cost. 
A bit-channel reliability estimation method for AWGN channels based on Gaussian approximation (GA) of density evolution, termed as DE/GA method, has been proposed in [7], giving accurate results with limited complexity. 
However, on-the-fly design increases coding latency too much to meet the 5G requirements.
```

```
Recent studies on the partial reliability order imposed by the polarization effect on bit-channels [8]–[10] opened new opportunities in the generation of an universal reliability sequence to be used independently on channel conditions. 
These studies and intensive simulations lead the 5G standardization to propose a unique universal bit-channel reliability sequence to be used as a basis to extract the individual reliability sequence for each polar code considered in 5G. 
This sequence, composed by 1024 bit-channel indices sorted in reliability order, can be used regardless the channel conditions to design the frozen set of any polar code of length smaller or equal to 1024: it is in fact possible to extract sub-sequences for shorter codes directly from the universal reliability sequence, hence reducing the sequence storing space. 
This nested reliability structure represents a real breakthrough in polar code design, and it is probably the most lasting legacy of the standardization process. 
This impressive result has been achieved by taking into account also distance properties in short polar codes design [11], [12], the use of list decoders and the presence of assistant bits in the code [13].
```

B. SC-based decoding    
```
Polar codes have been specifically conceived for SC decoding [1], with which they achieve channel capacity at infinite code length. 
Even if other decoding algorithms like BP [14] and SCAN [15] have been proposed, their poor error correction performance convinced 3GPP to focus the design envisaging an SC-based decoder at the receiver side. 
In the original formulation of SC, soft information was described in terms of likelihoods [1], that are numerically unstable and not suitable for hardware implementations. 
This instability has been reduced using log-likelihoods first [16], and finally eliminated with log-likelihood ratios (LLRs) [17]. 
The SC algorithm can be implemented in both software and hardware with low complexity [18], [19], however its error-correction performance is mediocre at practical code lengths. 
Thus, many attempts have been made to overcome this issue [20], [21].
```

```
Eventually, a list-based decoding approach to polar codes (SCL) was introduced in [22]. 
The idea is to let a group of SC decoders work in parallel, maintaining different codeword candidates, or paths, at the same time.
Every time a leaf node is reached, the bit is estimated as both 1 and 0, doubling the number of codeword candidates; a path metric for each candidate is then calculated to discard less likely candidates, hence limiting the number of paths. 
SCL substantially improves the error-correction performance of SC at moderate code lengths, especially when the code is concatenated to an outer code acting as a genie, e.g. a cyclic redundancy check (CRC), at the cost of an augmented complexity. 
CRC-assisted (CA) SCL has been used as a baseline in 5G error-correction performance evaluations, in particular with a list size equal to 8 [23]. 
```

```
While the effectiveness of SCL improves as the list size increases, its implementation complexity increases as well.
To limit the rise in complexity, various approaches have been proposed for software and hardware decoders alike. 
Partitioned SCL [24] and its evolutions [25], [26] consider different list sizes at different stages of the SC tree, reducing the memory requirements at higher stages. 
SC-Stack decoding [27] expands only the most probable candidate thanks to a priority queue. Adaptive SCL decoding [28] foresees increasing list sizes in case of failed decoding, while a hardware decoder with flexible list size has been proposed in [29].
```

C. Rate matching
```
By definition, the polar code length N is limited to powers of two, while any number K of bit-channels can be included in the information set. 
This code length constraint represents a limitation for typical 5G applications, where the amount of information A is fixed and a codeword of length E is needed to achieve the desired rate R = A/E. 
Rate matching for polar codes becomes thus a length matching problem, and has been solved for 5G through classical coding theory techniques as puncturing, shortening and extending [30].
```

```
Both puncturing and shortening reduce the length of a mother code by not transmitting code bits in a predetermined pattern, called matching pattern; the difference lies in the meaning of the code bits belonging to the matching pattern. 
In puncturing, the untransmitted code bits are treated as erased at the decoder, while shortening introduces a sub-code imposing the untransmitted code bits to assume a fixed value, typically zero, such that their value is already known by the decoder. 
It has been observed that for polar codes, shortening works better for high rates, and puncturing for low rates [31].
```

```
Puncturing U code bits deteriorates the bit-channel reliabilities, additionally causing U bit channels, termed as incapable bits, to be completely unreliable; the position of these bits can be calculated on the basis of the matching pattern [32].
In order to avoid catastrophic error-correction performance degradation under SC decoding, incapable bits must be frozen to prevent random decisions at the decoder. 
Shortening, on the contrary, improves the bit channel reliabilities by introducing overcapable bits, i.e. bits with conceptually infinite reliability that are always correctly decoded [31]. 
However, code bits in the matching pattern must depend on frozen bits only, which forces to freeze the most reliable bit-channels worsening the error correction capabilities of the code.
```

```
Rate matching alters the code reliability sequence so that bit-channel reliabilities need to be recalculated to find the optimal frozen set; DE/GA method is used to evaluate new bitchannel reliabilities in [33] for shortening and in [32], [34] for puncturing. 
The latency increase due to on-the-fly reliability calculation brought 3GPP to prefer an alternative approach, namely to design the matching pattern on the basis of the frozen set [35], [36]. 
This significantly reduces the code design complexity, albeit at the cost of an increased block error rate (BLER).
The joint optimization of frozen set and matching pattern, as proposed in [37], has been evaluated too, however it has been discarded due to its excessive complexity.
```

```
When the code length E is slightly larger than a power of two, a large number of bits would not be transmitted if puncturing or shortening were to be applied, since the mother polar code length would have to be selected as the successive power of two. 
In this case, extending a smaller polar code, i.e. retransmitting some code bits, may be preferable in terms of decoding latency and error correction performance [38] since a smaller mother polar code decoder can be used combining the likelihoods of retransmitted bits. 
The presence of repeated code bits due to retransmission alters the bitchannels reliability, however without giving restrictions to code design as for puncturing or shortening [36]. 
This allowed 3GPP to focus on the design of puncturing and shortening mechanism while leaving the repetition scheme as simple as possible.
As a result, a circular buffer rate matching [39] scheme has been adopted incorporating all three techniques in an elegant and simple way
```

D. Assistant bits design     
```
Assistant bits can be broadly defined as additional bits that help the decoding of the polar code, usually improving its error-correction performance. 
Their introduction dates back to the conception of SCL decoding, for which it has been noticed that the introduction of an outer CRC code improved the errorcorrection performance of the code [22], [40]. 
In general, it has been proven that the minimum distance of polar codes can be dramaticaly improved by adding an outer code [41], and SCL decoders can fully exploit this improved code spectrum [42]. 
Parity-checks (PC) have emerged as an alternative to CRC due to their simplicity and flexibility [41], [43]. 
Introducing PC bits in the middle of the decoding instead of a single CRC check at the end makes it easier to tune the polar code spectrum; bit-channels corresponding to minimum weight rows are the best candidates to host parity checks [44]. 
3GPP decided to adopt a low complexity PC design based on shift registers [45] in conjunction with a classical CRC, as proposed in [46], to improve the code design flexibility.
```

```
The insertion of an interleaver between the CRC encoder and the polar encoder may have a positive impact on the performance of the code due to the induced change in the number of minimum weight codewords [47]. 
The interleaver is used to turn the CRC into a distributed CRC, in that a CRC remainder bit is assigned to a bit-channel as soon as all the information bits involved in its parity check have been assigned as well. 
This feature is used in 5G polar codes to reduce the decoding complexity by early terminating the decoding if an incorrect check is met, providing an additional false alarm rate (FAR) mitigation mechanism [48].
```

E. Polar coded modulation    
```
If low-order modulation schemes like quadrature phase shift keying (QPSK) are used, the bit-channels polarization is not affected since all modulated symbols have uniform reliability. 
When larger constellations are used, as envisaged by 5G standard, polar-coded modulation (PCM) [49] can be used to fully exploit the polarization effect at higher-order modulation [50]. 
However, canonical PCM requires an additional polarization matrix whose size depends on the modulation scheme used [51], resulting in an increased decoding latency [52].
```

```
A channel interleaver has been introduced by 3GPP as a low-complexity alternative to PCM; this technique, termed as bit-interleaved polar-coded modulation (BIPCM) [53], improves the diversity gain under high-order modulation without increasing the code complexity [54].
In BIPCM, the channel is considered as a set of parallel bit channels which can be combined with polar coded bits. Carefully mapping coded bits to modulation symbols offers a certain gain over the conventional random interleaving [55], which can be further improved designing interleavers adaptive to channel selectivity [56]. 
Finally, the correlation among coded bits mapped into the same symbol allows to combine the demapping and deinterleaving units with the SC decoder to perform the decoding directly on the LLRs of the received symbols [57].
```


















