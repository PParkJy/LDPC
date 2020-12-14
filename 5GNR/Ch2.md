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
#### - &nbsp;&nbsp;&nbsp;&nbsp; 5G NR LDPC structure
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
- **bit 표현**   
  ```
  entire set of liftings can be represented by five 9-bit values and three 8-bit values (a 69-bit descriptor) per edge.
  ```    
  : 8bit는 0 ~ 255까지, 9bit는 0 ~ 511까지 표현 가능   
  : 리프팅 사이즈 Z 집합을보면 iLS=0, 1, 2, 4, 5 총 5가지 경우는 9bit으로 표현 가능하며 나머지 3 경우는 8 bit으로 표현 가능    
  : 별로 안중요한 것 같아요 호호호호호     
  
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
- **small base graph**    
  : 적은 수의 Variable Node(N) -> 작은 블록 길이
  : 높은 R을 위해 너무 작아서도 안됨
  : Density evolution 분석에 의하면 small base graph는 R은 줄어들지만 capacity와는 적은 차이 즉, 좋은 성능을 보인다고 함.    
  : 리프팅 후의 전체 블록 길이는 Z * N으로 계산되므로 base graph의 블록 길이(N)가 고정된다면 상대적으로 Z가 커질 수 있음    
- **Z와 병렬화**    
  : 리프팅 = copy + permutation 인데 만약 permutation이 cyclic permutation이라면 Quasi-Cyclic LDPC가 될 수 있음    
  &nbsp;&nbsp; -> Quasi-Cyclic code    
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 코드워드의 비트를 순환적으로 이동 (마지막 비트는 처음으로 이동)시킨 코드워드가 코드에 속한다면 cyclic code    
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 코드워드를 블록으로 나누고, 몇 개의 블록에서 순환적으로 이동시킨 코드워드가 코드에 속한다면 Quasi-Cyclic code    
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 리프팅 후의 Quasi-Cyclic code는 여러개의 circulant matrix (Z x Z)가 모여있는 형태    
  : circulant matrix의 각 열/행은 첫번째 열/행부터 shift된 것    
  : 즉, shift value만 알면 첫번째 열/행에서 마지막 열/행까지의 연결 관계를 파악할 수 있음    
  : 여러개의 circulant matrix의 첫번째 열/행들만 모아서 디코딩하는 프로그램을 만든다면 shift value에 의해 다음 열/행들의 연결관계를 파악할 수 있으므로 똑같은 프로그램을 재사용 가능    
  : circulant matrix의 크기가 ZxZ이므로 총 Z개의 프로그램을 동시에 돌릴 수 있으므로 Z개로 병렬화 가능    
  : 병렬화에 따라 대기 시간(latency)가 감소하며, 처리량(throughput)은 늘릴 수 있음    
- **정리**    
  : small base graph를 사용하면 Z가 커지는 효과가 있고 그에 따라 많은 병렬처리를 할 수 있기에 low latency, high throuput의 장점이 있으나 code rate은 감소할 수 있다.    
   
</br>

```
Accordingly, 3GPP has agreed to consider two rate-compatible base graphs, BG1 and BG2, for the channel coding. 
BG1 is targeted for larger blocklengths (500 ≤ K ≤ 8448) and higher rates (1/3 ≤ r ≤ 8/9), whereas BG2 is targeted for smaller blocklengths (40 ≤ K ≤ 2560) and lower rates (1/5 ≤ r ≤ 2/3). 
The range of kb is [19, 22] and [6, 10] for BG1 and BG2, respectively. 
The maximum Z for BG2 is limited to 256; extending this to 384 would make better use of available hardware and is under consideration. 
More generally, a wider range of supported kb values would allow a wider range of code rates and block sizes to utilize full hardware parallelism, but this idea was not pursued in 3GPP.
```
- **2개의 Base Graph**    
  : 서로 다른 요구사항을 고려하는 Base Graph 2개가 제안됨    
- **BG1**
  : 큰 블록길이 (500 ≤ K ≤ 8448)     
  : 높은 R (1/3 ≤ r ≤ 8/9)    
  : information block length k_b 19 ~ 22     
  : 최대 Z=384     
- **BG2**     
  : 작은 블록길이 (40 ≤ K ≤ 2560)     
  : 낮은 R (1/5 ≤ r ≤ 2/3)    
  : information block length k_b 6 ~ 10    
  : 최대 Z=256 (384로 늘려서 하드웨어 병렬화 고려 중)     
- **etc**     
  : 지원가능한 information block length k_b의 범위가 넓어진다면 더 다양한 R과 블록 사이즈를 사용할 수 있고, 하드웨어 병렬 처리에도 활용할 수 있을 것이라고 한다.       

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
- **BG1**  
  : BG1과 BG2는 비슷한 구조를 가지고 있는데 논문에서는 더 높은 R을 제공하는 BG1을 중점으로 설명    
  : 현재는 information block length k_b를 22만 지원 -> 즉, k_bmax = 22이고 다음에 설명하는 바에 따르면 이보다 작은 k_b도 사용 가능할 것이라 함     
  : sub-column shortening을 사용한다면 하나의 Z에 대해 21 * Z ~ 22 * Z라는 information block length k_b가 지원될 수 있음    
  &nbsp;&nbsp; -> sub-column shortening
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : base graph의 가장 마지막 블록을 shortening    
  : 앞에서 봤던 것처럼 많은 Z가 있긴 한데 사용자가 원하는 모든 k_b의 범위를 지원할 수는 없음     
  &nbsp;&nbsp; -> 예를 들면, Z가 2일 땐 k_b 범위가 42 ~ 44이고, 3일 땐 63~66이므로 44 ~ 63만큼의 k_b는 만들 수 없단 것!     
  : 더 많은 범위를 지원할 수 있게 하기 위해 base graph에 추가적인 shortening을 하는 방법이 제안됨     
  &nbsp;&nbsp; -> additional base graph shortening     
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 8보다 큰 Z에 대해서 Z1과 Z1보다 작은 Z 중 가장 큰 Z2가 있을 때 (즉, 리프팅 사이즈 Z 리스트를 오름차순으로 정렬했을 때 Z2 다음에 Z1이 나오는 것) Z1과 Z2의 비율을 계산해봤을 때 가장 큰 비율이 9/8이었다고 함          
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 사용자가 원하는 리프팅 후의 information block length K가 있을 때 k_bmax가 22이므로 K ≤ 22 * Z를 만족하는 가장 작은 Z를 찾아야 함! (어차피 더 큰 Z를 써봤자 낭비일 뿐....)     
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 이 때 찾고자 하는 Z라고 한다면 22 * Z2 ~ 22 * Z1 사이에 K가 있다고 볼 수 있음. 즉, Z = Z1이 되는 것! (왜냐면 Z2와 Z1은 정해진 게 아니라 걍 Z 리스트에서 연속된 순서인 두 Z를 가져오는 것이기 때문에 8보다 큰 Z들에 대해 K가 어느 Z2와 Z1 사이 구간에 속하는 건지 확인하는 것이므로!)    
  : 하지만 Z는 K보다 크므로 shortening이 들어가게 되는데 이 때 Z1과 Z2의 최대 비율이 9/8이었다는 건 Z2가 Z1의 8/9배라는 것을 말하므로 22 * Z1 * 1/9 정도 shortening하면 K를 얻을 수 있는 것! 
  ![image](https://user-images.githubusercontent.com/46422952/102087867-79b94e00-3e5d-11eb-8bac-88727e4589f3.png)    
  : base graph가 현재 BG1의 k_b이자 k_bmax인 22보다 더 작은 19, 20, 21에도 좋은 성능을 보이도록 설계된다면 좋은 결과를 기대할 수 있을 것이라 함    
  : 즉, BG2처럼 유연한 디자인을 할 수 있게 되고 이는 이전 설계와의 차이점이라 함    
  

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
- **BG1의 구조**    
  ![image](https://user-images.githubusercontent.com/46422952/102083278-a453d880-3e56-11eb-995f-1580bd09bae9.png)     
  : 대부분 [여기](#5G-NR-LDPC-structure)서 설명한 것과 같음!    
  : [여기](#5G-NR-LDPC-structure)의 extension (1)은 (2)의 패리티 부분 (즉, core의 Variable Node의 부분집합의 패리티)     


</br>

```
As described above, the total number of base information columns is kbmax. 
Shortening at the base graph level is achieved by eliminating the right-most systematic columns so that the kb of the base graph in 5G NR is an adjustable parameter. 
The design of both the core and the IR-HARQ portion should be done so as to provide good performance. 
In the next section we describe some of the design details that enable this flexible design to yield very good code performance across the full range of block lengths and code rates.
```
- **요약**    
  : base graph의 전체 information block length는 k_bmax.    
  : 하지만 base graph에서 shortening을 사용한다면 information block length k_b는 조절가능한 변수가 되며, 이 shortening은 가장 우측의 systematic한 열들을 삭제하는 것과 같음    
  : 또한 core와 IR-HARQ를 지원하는 extension 부분 즉, 모든 부분에서 좋은 성능을 보이도록 설계되어야 함    

</br>
</br>

## Reference
[1] J. Hagenauer, "Rate-compatible punctured convolutional codes (RCPC codes) and their applications," in IEEE Transactions on Communications, vol. 36, no. 4, pp. 389-400, April 1988.   
[2] https://en.wikipedia.org/wiki/Hybrid_automatic_repeat_request  
