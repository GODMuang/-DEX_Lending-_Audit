
# DEX_Audit-jacqueline
## 대상
|Name |DEX_solidity|
|--|--|
|Target/Version|Git Repository(DEX_solidity): commit a5981c8297cdee9ab8aae232a32743319445efe5 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/je1att0/DEX_solidity

   

# #1 올바르지 못한 비율의 유동성 추가
## 설명
src/Dex.sol : line No. 25 위치의 ```addLiquidity()```이 호출되어 유동성이 추가될 때, 현재 유동성 풀과 다른 비율의 유동성을 집어넣으면 문제가 발생한다. 코드에서는 ```lpReturn``` 변수를 이용해 추가된 유동성중에 더 적은 양의 토큰을 기준으로 lp 토큰을 발행하여 풀 내의 비율을 유지하고자 하지만, 풀에 추가되고 남은 토큰을 다시 사용자에게 돌려주지 않음에 있어서 문제가 발생할 것으로 보인다.


## 파급력 
```Low ```   
lp를 발행하고 남은 토큰을 돌려주지 않게되면서 ```addLiquidity()```를 오용한 사용자 본인에게 손해를 끼치는 버그입니다. 그와 더불어 남은 토큰이 그대로 풀에 추가되게 되면서, 실제 토큰 비율에도 영향을 미쳐 서비스에 영향을 주게됩니다.

## 해결방안
토큰을 풀에 추가하기 전 또는 후에 사용자에게 남은 토큰을 돌려준다.

---   
<br>
<br>
<br>




# #2 유동성 제거 시 올바르지 못한 토큰 반환
## 설명
src/Dex.sol : line No. 80 위치의 ```removeLiquidity()``` 가 호출되어 lp토큰을 반환할 때, 사용자 본인이 실제 소유한 lp인지 검증하지 않음.   
따라서 유동성 풀에 자금이 있으면 ```removeLiquidity()```를 마음대로 호출하여 타인의 자금까지도 모두 탈취가능함.


## 파급력 
```Critical ```   
유동성을 전혀 공급하지 않고도 풀의 토큰을 모두 인출해낼 수 있다는 점에서 매우 치명적인 것으로 생각됨.

## 해결방안
```removeLiquidity()```호출 시에 사용자의 lp인지 검증하는 로직을 추가해야함.