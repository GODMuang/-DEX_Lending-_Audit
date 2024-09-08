
# DEX_Audit-icarus
## 대상
|Name |DEX_solidity|
|--|--|
|Target/Version|Git Repository(DEX_solidity): commit df61bf1d26cf2d0ec6059c2cacc83e6bbffb45e5 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/pluto1011/-Lending-DEX-_solidity/

   

# #1 올바르지 못한 비율의 유동성 추가
## 설명
src/Dex.sol : line No. 30 위치의 ```addLiquidity()```이 호출되어 유동성이 추가될 때, 현재 유동성 풀과 다른 비율의 유동성을 집어넣으면 문제가 발생한다. 코드에서는 ```liquidity``` 변수를 이용해 추가된 유동성중에 더 적은 양의 토큰을 기준으로 lp 토큰을 발행하여 풀 내의 비율을 유지하고자 하지만, 풀에 추가되고 남은 토큰을 다시 사용자에게 돌려주지 않음에 있어서 문제가 발생할 것으로 보인다.



## 파급력 
```Low ```   
lp를 발행하고 남은 토큰을 돌려주지 않게되면서 ```addLiquidity()```를 오용한 사용자 본인에게 손해를 끼치는 버그입니다. 그와 더불어 남은 토큰이 그대로 풀에 추가되게 되면서, 실제 토큰 비율에도 영향을 미쳐 서비스에 영향을 주게됩니다.

## 해결방안
토큰을 풀에 추가하기 전 또는 후에 사용자에게 남은 토큰을 돌려준다.

