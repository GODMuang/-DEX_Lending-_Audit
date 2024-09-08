
# Lending_Audit-hakid
## 대상
|Name |Lending_solidity|
|--|--|
|Target/Version|Git Repository(Lending_solidity): commit 25c37d56033fd27a3f5fd772223ff2552438d8ab (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/hakid29/Lending_solidity

   

# #1 재진입 공격 가능성
## 설명
src/UpsideAcademyLending.sol : line No. 155 위치의 ```withdraw()```함수에서 Reentrancy를 이용한 공격 가능성이 보입니다. 
```
src/UpsideAcademyLending.sol : line No. 166 ~ 168
payable(msg.sender).transfer(amount);
user_.etherDeposit -= amount;
Users[matchUserId[msg.sender]-1] = user_;
```   
```
src/UpsideAcademyLending.sol : line No. 172 ~ 175
ERC20(usdc).transfer(msg.sender, amount);
totalUSDCDeposits -= amount;
user_.usdcDeposit -= amount;
Users[matchUserId[msg.sender]-1] = user_;
```
코드에서는 토큰과 이더 모두 자금전송 후에 상태 업데이트를 진행하게 되는데 이 부분에서 추후 문제가 발생할 수 있을 것으로 보입니다.   
이더 전송의 경우 현재로썬 ```call()```이 아닌 ```transfer()```을 이용하여 Reentrancy가 발생하진 않았고, 현재 거래소에서 수용되는 토큰의 경우에도 reentrancy가 허용되는 구조는 아닙니다. 다만, 추후 유지보수 이후에 발생할 수 있는 공격벡터를 줄이기 위해 CEI 패턴을 따르는 것을 권장합니다.


## 파급력 
```Informational ```   
현재로썬 파급력 없음.    
다만, ```call()```을 이용하여 이더를 전송하거나, ERC777표준을 따르는 토큰이 추가되었을 때, 공격 가능성이 존재합니다.

## 해결방안
CEI 패턴을 따른다.


