# Preliminaries

```
In this section, we describe the fundamental concepts necessary to familiarize with the structure, the encoding and the decoding of polar codes. 
This section can be skipped by readers having basic knowledge on the field, while we refer readers interested in further details and examples to [3].
```
A. Channel polarization    
```
The channel polarization phenomenon, introduced in [1], consists of a transformation which produces N “synthetic” bit-channels from N independent copies of a binary-input discrete memoryless channel (B-DMC). 
The new synthetic channels are then “polarized” in the sense that each of them can transmit a single bit at a different reliability, i.e. with a different probability of being decoded correctly. If N is large enough, the mutual information of the synthetic channels is either close to 0 (completely noisy channels) or close to 1 (perfectly noiseless channels), resulting in channels of extreme capacities. Polar codes provide a low-complexity scheme to construct polarized channels, where the fraction of noiseless channels tends to the capacity of the original B-DMC.
```

```
The mathematical foundations of polar codes lie in the discovery of the polarization phenomenon of matrix G2 = [ 1 0 / 1 1 ], also known as basic polarization kernel. This matrix enables the encoding of a two-bits input vector u = [u0, u1] into code word d = [d0, d1] as d = u · G2, namely having d0 = u0 ⊕ u1 and d1 = u1. 
The polarization effect brought by G2 is more evident for binary erasure channels (BECs), where the transmitted bit is either received correctly or lost with probability δ. 
If the input bits are decoded sequentially, bit u0 cannot be recovered as u0 = d0 ⊕ d1 if any of the code bits has not been received, hence with probability δ0 = (2−δ)δ > δ. 
After u0 has been correctly decoded, input bit u1 can be decoded either as u1 = d1 or u1 = u0 ⊕ d0, hence it is sufficient that at least one of the two code bits has been received: consequently, failed decoding of this bit happens with probability δ1 = δ 2 < δ. As a result, input bit u0 is transmitted over a degraded synthetic BEC with erasure probability δ0 > δ, while u1 is transmitted over an enhanced synthetic BEC with erasure probability δ1 < δ.
```
B. Code design    
```
Polar codes are based on the concatenation of several basic polarization kernels, creating a cascade reaction that speeds up the synthetic channels polarization while limiting encoding and decoding complexity. 
This concatenation generates a channel transformation matrix of the form GN = G2 ⊗n , defined by the n-fold Kronecker product of G2, and that can be recursively calculated as GN = [GN/2 0 | GN/2 GN/2]. While for n → ∞ this construction creates channels that are either perfectly noiseless or completely noisy, for smaller values of n the synthetic channels polarization may be incomplete, generating intermediary channels that are only partially noisy. 
Figure 2 shows the bit-channels polarization enabled by the concatenation of n = 10 basic polarization kernels for a BEC with erasure probability δ = 1/2.
```
C. Encoding     
```
By construction, polar codes only allow for code lengths that are powers of two, in the form N = 2n; on the other hand the code dimension K, i.e. the number of information bits transmitted, can take any arbitrary value.
The goal of code design of an (N, K) polar code is to identify the K best synthetic channels, namely the channels providing the highest reliability, and use them to transmit the information bits. 
The estimation of the reliability of each synthetic channel allows to sort them in reliability order and assign the K information bits to the most reliable channels, whose indices constitute the information set I of the code. 
The remaining N − K indices form the frozen set F = I ^C of the code, and the respective channels are “frozen”, not carrying any information.
```
```
An (N, K) polar code is defined by its channel transformation matrix GN = G2 ⊗n and its information set I. 
The generator matrix of the code is given by the sub-matrix of GN composed by the rows whose indices are stored in I. 
However, the recursive structure of GN allows to reduce the encoding complexity with the introduction of an auxiliary input vector u of length N. 
This input vector u = [u0, u1, . . . , uN−1] is generated by assigning ui = 0 if i ∈ F, and storing the information bits in the remaining entries. Codeword d = [d0, d1, . . . , dN−1] is then calculated as d = u · G_N. 
This matrix multiplication can be performed by processing multiple G2 matrix multiplications in parallel, reducing the encoding complexity to O(log2(N)). 
In fact, thanks to the recursive structure of GN, the decoding can be performed in log2(N) steps, each one composed by N/2 identical basic polarization kernels. 
Figure 3 shows the structure of the encoder of a polar code of length N = 8; moreover, it details the erasure probabilities of the synthetic BECs when the code is designed for a BEC of erasure probability δ = 1/2, along with the frozen set F = {0, 1, 2, 4} obtained for a code of dimension K = 4. 
This structure is used to encode message c = [1, 0, 1, 1] in n = 3 steps with the assistance of input vector u = [0, 0, 0, 1, 0, 0, 1, 1] to obtain codeword d = [1, 0, 1, 0, 0, 1, 0, 1].
```
D. Decoding     
```
In [1], the decoding algorithm native to polar codes, called successive cancellation (SC), has been proposed as well. 
It can be represented as a depth-first binary tree search with priority to the left branch, where the leaf nodes are the N bits to be estimated, and soft information on the received code bits is input at the root node, with a decoding complexity of O(Nlog2(N)). Figure 4 shows the decoding tree of the (8, 4) polar code depicted in Figure 3, with black leaf nodes representing information bits and white ones frozen bits. 
Taking a node at stage t as a reference, the message flow can be recursively described as shown in Figure 5. 
The node uses the 2 t soft inputs α received from its parent node to calculate 2 t−1 soft outputs α` as α ` i = f(αi , αi+2t−1 ) to be transmitted to its left child; later, it will combine the 2 t−1 hard decisions β ` received from its left child with α to calculate 2 t−1 soft outputs αr for its right child as α r i = g(αi , αi+2t−1 , β` i ). 
Finally, the 2 t−1 hard decisions β r received from its right child are combined with β ` to calculate 2 t hard decisions β to be transmitted to its parent node as βi = β ` i ⊕β r i if i < 2 t−1 and βi = β r i−2 t−1 otherwise. When a leaf node is reached, the soft information is used to take a hard decision on the value of the information bits; frozen bits are always decoded as zeros. 
```
```
Update rules f and g for left and right child nodes respectively depend on the channel model. In the case of BEC, soft values belong to the set {0, 1, e}, with e representing an erasure, while hard decisions can only take values 0 and 1. 
Update rules are f(α1, α2) = α1 ⊕ α2 and g(α1, α2, β) = α2 ∨(α1 ⊕β); the erasure algebra imposes that e⊕ • = e and e ∨ • = •. Continuing with the example of Figure 3, Figure 4 shows the flux of soft and hard messages passed along the decoding tree if soft vector y = [e, 0, e, 0, e, 1, 0, 1] has been received, allowing to correctly decode the message.
```