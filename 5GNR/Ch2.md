# Meeting NR Block Length and Rate Requirements

## 0. Intro
```
The channel coding solution for NR needs to be rate-compatible to support IR-HARQ. 
The larger transmission frames needed for evolved mobile broadband (eMBB) warrant the use of large blocklengths for performance gains. 
On the other hand, single resource block allocations may lead to code blocklengths on the order of a few hundred coded bits. 
Thus, information blocklength sizes ranging from around 100 to 8000 need to be supported. 
A coding rate of 8/9 was chosen to meet the peak decoder throughput of 5 Gb/s, while a rate of 1/5 is needed to achieve extended coverage and meet the 100 Mb/s edge cell throughput goal.
```
- **rate-compatible[1]**    
  : 채널 등의 요구에 따라 코드의 rate(R = K/N)를 바꿀 수 있는 것    
  : 레이트가 변함에 따라 오류 정정을 위한 패리티 체크의 수가 변하므로 정정 능력에 영향을 미침     
  : high rate의 코드가 low rate의 코드에 속해있게 되는 형태     
- **IR-HARQ[2]**    
  : Incremental Redundancy-Hybrid ARQ = FEC(Forward Error Correction) + ARQ(Automatic Repeat reQuest)    
  : FEC의 경우 잘 알고 있는 Error Correction Code와 같은 것, OSI 1계층 수준에서 처리    
  : ARQ는 수신측에서 오류를 발견하여 송신 측에 재전송을 요구하는 방식으로 신뢰성 확보, OSI 2계층 수준에서 처리    
  : IR-HARQ는 재전송 요청을 받은 송신 측이 redundancy라고 불리는 추가 정보를 보내고, 수신 측에 redundancy와 이전에 수신한 데이터를 합쳐서 다시 디코딩    
- **eMBB**    
  : enhanced Mobile BroadBand    
  : eMBB는 고속 통신과 이동성 등을 제공할 수 있다는 5G 시나리오    
  : 성능 이득을 위해 큰 블록 길이를 사용하여 큰 전송 프레임을 보내는 경우도 있고, 몇백개 정도의 비트만 사용해서 단일 블록만 사용하는 경우도 있음 => 다양한 전송 상황 존재       
  : 즉, information 블록 길이를 100 ~ 8000 정도로 다양하게 지원해야 하는 것이 필요    
  : 예) 핫스팟, 큰 용량을 다운받는 것같이 5Gb/s 정도의 높은 처리량을 요구할 때는 R=8/9, 이동성을 고려할 때는 처리 속도가 100Mb/s로 떨어지지만 넓은 셀 영역을 커버할 수 있도록 R= 1/5    
- **정리**    
  : 5G NR에서는 eMBB 시나리오같이 서로 다른 통신 요구사항을 만족시키기 위해 information 블록의 사이즈를 다양하게 지원할 수 있어야 하며 이에 상응하는 채널 코딩이 IR-HARQ를 지원하는 rate-compatible한 솔루션이 되도록 해야한다는 것     
  
</br>

```
Supporting a wide range of code rates naturally aligns with the need to support IR-HARQ. 
For example, a code with information blocklength K = 800 and rate 8/9, so N = 900, may need an IR-HARQ extension to N = 2400. 
That extension provides for a rate 1/3 code.
Very often intermediate IR-HARQ extensions are also required, so the code should have good performance for rates from 8/9 down to 1/3. 
A goal in the LDPC design is to have good performance across IR-HARQ extensions so that the design of the highest rate codes together with their IR-HARQ extensions inherently provide good codes for all desired rates.
```
- **LDPC 설계의 목표**    
  : 사용자가 원하는 모든 속도를 지원할 수 있는 좋은 코드 제공    
  : IR-HARQ를 지원하기 위해 확장(extension) 되는 데이터에도 좋은 성능을 보이는 것    
  : 예) K = 800, N = 900, R=8/9 -> 에러 발견 및 재전송 요청 -> 송신측에서 추가 redundancy 전송 -> N=2400, R=800/2400=1/3    
 
</br>

```
The 5G NR LDPC codes are, strictly speaking, a concatenation of an LDPC code and a low-density generator matrix (LDGM) code. 
The structure begins with a relatively high rate “core”; this is the LDPC part. 
The base graph for the core has a small number (e.g., mcore = 4) of parity checks and some number (e.g., kbmax = 22) of information variable nodes and mcore parity variable nodes. 
All additional variable nodes are extension degree one variable nodes each connected to a unique check node whose other variable node neighbors are taken from the core; this is the LDGM part. 
In general, the degree one variable nodes are the extension nodes used for IR-HARQ but the first degree one variable node is special for reasons discussed below, and is included in all code rates. 
For structural reasons that we describe shortly, the core portion of the graph does not perform very well without including at least some of the first degree one parity bits.
```
- **5G NR LDPC**    
  : 5G의 LDPC는 정확하게는 LDPC와 LDGM이 연접(concatenation)된 코드    
  : LDGM(Low-Density Generator Matrix) = LDPC 계열, 아래 사진 참고    
  ![image](https://user-images.githubusercontent.com/46422952/102057109-c0448380-3e30-11eb-8b01-a10e7e306276.png)    
- **5G NR LDPC 구조**    
  ![image](https://user-images.githubusercontent.com/46422952/102058283-a146f100-3e32-11eb-9661-1661818c429b.png)     
  1. core    
    : LDPC 부분    
    : information(k_bmax개의 column) + parity (m_core개의 column)    
    : 대부분 information으로 구성되므로 high rate    
  2. extension    
    : LDGM 부분    
    : rate-compatible과 IR-HARQ를 위한 부분    
    : Check node가 degree 1인 Variable node와 연결된 부분(1) + Check node가 core의 Variavle node와 연결된 부분(2)    
    : (1) 부분은 IR-HARQ를 위한 확장 노드로 사용되는데 첫번째 row의 첫번째 column에 위치한 (1)의 첫번째 Variable Node는 특별하다고 함    
    &nbsp;&nbsp; -> core 부분만을 사용하면 성능이 좋지 않다고 알려져 있기 때문에 적어도 몇 개의 (1)부분을 포함시켜야 하므로 (1)의 첫번째 Variable Node는 항상 R 계산에 사용됨    

</br>

```
One aim of the 5G design was to have a compact description for many code blocklengths, and one important enabling idea is to have many Z liftings defined for a single base graph.
Associated to each edge in the base graph is a descriptor so that given a target lifting value of Z, the associated lifting value for the edge is derivable from the descriptor. 
There are many ways to accomplish this, but we limit ourselves to a description of the method selected in 3GPP.
```
- **5G 설계 목표**    
  : 위에서 언급했듯이 5G NR에서는 코드의 블록길이가 다양하게 지원되어야 함     
  : 이 코드 블록 길이들을 하나씩 설명하는 게 아니라 간단하게 설명할 수 있도록 설계하는 것이 목표 중 하나!    
- **base graph**    
  : 많은 코드를 설계하기 위해 base graph라는 개념 도입    
  : H의 base graph -> lifting -> 실제 H의 Tanner graph   
- **lifting**
  ![image](https://user-images.githubusercontent.com/46422952/102076304-e0813c00-3e4a-11eb-8701-9b4eb7560b68.png)    
  : base graph를 사용하여 H를 만드는 과정     
  : copy + permutation으로 구성됨 
  &nbsp;&nbsp; 1) copy    
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : lifing size Z만큼 기존의 엣지들 복제    
  &nbsp;&nbsp; 2) permutation    
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : shift value라는 값만큼 연결 관계를 이동시키는 것    
  : 논문에서는 base graph를 desciptor라고 표현하면서 base graph와 Z를 사용해서 엣지를 위한 어떤 값(사실 shift value)을 계산할 수 있다고 함    
  
</br>

```
The set of lifting sizes supported for the graph are all values of the form Z = A x 2^j for A = {2, 3, 5, 7, 9, 11, 13, 15} and j = 0,1,… and includes all such Z in the range from 2 to 384. 
This includes Z = 2^j x {2, 3, 5, 7, 9, 11, 13, 15} for j = 0, 1, 2, 3, 4, 5 excluding those values exceeding Z = 384. 
Thus, the full lifting set Z is given by {2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 18, 20, 22, 24, 26, 28, 30, 32, 36, 40, 44, 48, 52, 56, 60, 64, 72, 80, 88, 96, 104, 112, 120, 128, 144, 160, 176, 192, 208, 224, 240, 256, 288, 320, 352, 384}. 
It is not clear that all lifting values, especially the small ones, will be used, but all are defined. 
The lifting values are organized into eight sets, one for each value of A. 
For each A, there is maximal Z(A) = A2^j for some maximal j = j(A). 
For each edge E in the base graph and each A, an integer value L(A, E) in the range [0, Z(A) – 1] is specified. 
Then, for a target lifting size Z = A2^j , the lifting value associated with the edge E is given by L(A, E) modulo Z. 
Thus, the entire set of liftings can be represented by five 9-bit values and three 8-bit values (a 69-bit descriptor) per edge.
```
- **lifting size Z**     
  : Z = A * 2^j, 2 <= Z <= 384    
  : A = {2, 3, 5, 7, 9, 11, 13, 15}    
  : j = 0 이상의 정수 (실제로는 Z의 제한때문에 j <= 0 <= 7)    
  : 각 A에 대해 계산되는 Z(A) = A * 2^j, 범위 내에서 최대인 Z(A)를 만드는 j가 존재 (ex: A=3 -> 최대 Z(A)를 만드는 j=7)    
  ![image](https://user-images.githubusercontent.com/46422952/102077395-95682880-3e4c-11eb-9157-c9f3a760a951.png)     
  : 참고로 작은 Z들이 실제로 사용되는지는 확실하진 않지만 일단 정의는 되어있음        
- **shift value 계산**    
  : base graph의 각 엣지 E와 A 사이에는 0 ~ Z(A) - 1의 값을 가지는 L(A,E)라는 정수값이 매칭되어 있음    
  : 정확한 설명을 위해 논문의 A와 일대일 대응 관계인 i_LS라는 5G 표준문서의 용어를 사용해서 재설명    
  : Z는 i_LS에 따라 8개의 집합으로 분류됨 -> 즉, Z가 정해진다면 i_LS를 정할 수 있음           
  : i_LS를 기준으로 엣지마다 정해져있는 L(A,E)를 찾을 수 있음    
  : L(A,E) mod Z = shift value (논문에서는 lifing value associated with the egde라고 표현됨)    
- **bit 표현 (이해안됨ㅎ)**   
  ```
  entire set of liftings can be represented by five 9-bit values and three 8-bit values (a 69-bit descriptor) per edge.
  ```    
  : 대체 무슨 말인지..?    
  
</br>
</br>

## 1. Multiple Base Graph
```
Although the above described eight PCMs would suffice to meet the requirements of 5G NR, there is room for improvement in latency and throughput at smaller blocklengths by introducing smaller base graphs with a smaller number of variable nodes. 
For a given blocklength, a relatively smaller base graph size implies a relatively larger Z and hence more parallelism in decoding operations. 
More parallelism in decoding implies lower latency and higher throughput. 
To achieve high code rate, however, the base graph cannot be too small. 
DE analysis predicts that smaller base graphs can perform well, with small gaps in capacity, although with reduced maximum code rate.
```
- ?

</br>

```
Accordingly, 3GPP has agreed to consider two rate-compatible base graphs, BG1 and BG2, for the channel coding. 
BG1 is targeted for larger blocklengths (500 ≤ K ≤ 8448) and higher rates (1/3 ≤ r ≤ 8/9), whereas BG2 is targeted for smaller blocklengths (40 ≤ K ≤ 2560) and lower rates (1/5 ≤ r ≤ 2/3). 
The range of kb is [19, 22] and [6, 10] for BG1 and BG2, respectively. 
The maximum Z for BG2 is limited to 256; extending this to 384 would make better use of available hardware and is under consideration. 
More generally, a wider range of supported kb values would allow a wider range of code rates and block sizes to utilize full hardware parallelism, but this idea was not pursued in 3GPP.
```
- ? 

</br>
</br>

## 2. Base Graph Shortening For Block Length Granularity
```
Base graphs BG1 and BG2 have similar structure. 
We focus our description on BG1, which is the main 5G NR high-rate base graph. 
Base graph BG1 has 22 information bit columns. 
Using sub-column shortening, as discussed above, we see that any information block length in the range 21*Z to 22*Z for any of the above Z values can be supported. 
Even with the large set of Z indicated above, however, this does not cover the entire desired range. 
To complete the coverage, additional shortening at the base graph level is introduced.
Note that for Z greater than 8, the largest ratio of a value of Z to the next smallest Z is 9/8.
Thus, for a sufficiently large target information blocklength K, if Z is the smallest supported Z such that K ≤ 22*Z, the code with information blocklength 22*Z can be shortened to information blocklength K by shortening at most (1/9)*22*Z = 2.44 Z bits. 
Good performance can be expected if the base graph is designed to give good performance not just for kb = kbmax = 22 but also for kb = 19,20,21. 
This kind of design flexibility distinguishes the 5G NR LDPC from previous designs.
```
- ?

</br>

```
The specific base graph structure adopted for 5G NR in 3GPP is illustrated in Fig. 5 as a sketch of the BG1 PCM where a 1 in the matrix indicates the presence of a base edge. 
The columns are broadly partitioned into three parts: information columns, core parity columns, and extension degree one parity-bit columns. 
In a lifted graph with lifting size Z, each column corresponds to Z variable nodes. 
In that case each 1 entry is replaced by an integer modulo Z to create a lifted PCM representation of the quasi-cyclic LDPC code. 
The rows are broadly partitioned into two parts: core check rows and extension check rows.
The bits in the core parity columns are solved so that the core parity checks are satisfied. 
Each degree one parity bit is then simply a parity of a subset of core variable nodes.
```
- ?

</br>

```
As described above, the total number of base information columns is kbmax. 
Shortening at the base graph level is achieved by eliminating the right-most systematic columns so that the kb of the base graph in 5G NR is an adjustable parameter. 
The design of both the core and the IR-HARQ portion should be done so as to provide good performance. 
In the next section we describe some of the design details that enable this flexible design to yield very good code performance across the full range of block lengths and code rates.
```
- ?

</br>
</br>

## Reference
[1] J. Hagenauer, "Rate-compatible punctured convolutional codes (RCPC codes) and their applications," in IEEE Transactions on Communications, vol. 36, no. 4, pp. 389-400, April 1988.   
[2] https://en.wikipedia.org/wiki/Hybrid_automatic_repeat_request  
