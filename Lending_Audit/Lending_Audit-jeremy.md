
# Lending_Audit-jeremy
## 대상
|Name |Lending_solidity|
|--|--|
|Target/Version|Git Repository(Lending_solidity): commit 544c3aff1cac5dd1f996e451c0fcfcd93a274843 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/WOOSIK-jeremy/Lending_solidity

   

# #1 올바르지 못한 변수의 혼용
## 설명
src/UpsideAcademyLending.sol : line No. 44 위치의 ```borrow()```호출 시 가격정보는 token 변수에서 받고 자금전송은 usdc로 이루어짐.
가격정보는 ```currentPrice``` 변수에 반영되어 담보비율을 계산하게 되어, 적정 담보비율에 맞지 않는 대출이 이루어질 수 있음.


## 파급력 
```Critical ```   
공격자가 담보비율을 임의로 설정하여 검증과정을 우회하고, 과도한 대출을 받아 자금을 탈취할 우려가 존재함.

## 해결방안
함수 내의 토큰변수를 하나로 통일시킴.
예 ) ```oracle.getPrice(token);```을 ```oracle.getPrice(usdc);```로 수정하는 등.

