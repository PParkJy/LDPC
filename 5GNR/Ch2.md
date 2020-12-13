# Meeting NR Block Length and Rate Requirements

0. 
```
The channel coding solution for NR needs to be rate-compatible to support IR-HARQ. 
The larger transmission frames needed for evolved mobile broadband (eMBB) warrant the use of large blocklengths for performance gains. 
On the other hand, single resource block allocations may lead to code blocklengths on the order of a few hundred coded bits. 
Thus, information blocklength sizes ranging from around 100 to 8000 need to be supported. 
A coding rate of 8/9 was chosen to meet the peak decoder throughput of 5 Gb/s, while a rate of 1/5 is needed to achieve extended coverage and meet the 100 Mb/s edge cell throughput goal.
```
- rate-compatible[1]    
- IR-HARQ



</br>

## Reference
[1]: J. Hagenauer, "Rate-compatible punctured convolutional codes (RCPC codes) and their applications," in IEEE Transactions on Communications, vol. 36, no. 4, pp. 389-400, April 1988.    
