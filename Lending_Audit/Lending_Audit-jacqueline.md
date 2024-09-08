
# Lending_Audit-jacqueline
## 대상
|Name |Lending_solidity|
|--|--|
|Target/Version|Git Repository(Lending_solidity): commit 7bb6357b7e5742133581be4acafe811bbbe3ee77 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/je1att0/Lending_solidity

   

# #1 올바르지 못한 토큰 withdraw 검증
## 설명
src/UpsideAcademyLending.sol : line No. 153 위치에서 ```withdraw()```함수를 이용해 토큰을 반환받을 때 추가적인 검증과정이 존재하지 않음.
토큰을 전송하기 전, 사용자 소유의 토큰인지 검증해야함. 현재 코드에는 토큰소유 검증을 하지 않아 타인의 토큰까지 강제로 인출할 수 있음.


## 파급력 
```Critical ```   
공격자가 거래소 내 모든 토큰을 무단으로 인출할 수 있음. 

## 해결방안
코드 내 ```accounts[].depositedUSDC``` 변수를 활용하여 토큰 소유 검증과정이 필요함.



