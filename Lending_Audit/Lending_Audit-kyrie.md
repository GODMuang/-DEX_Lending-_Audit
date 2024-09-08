
# Lending_Audit-kyrie
## 대상
|Name |Lending_solidity|
|--|--|
|Target/Version|Git Repository(Lending_solidity): commit 7c149b1257ebb4ec30925da15c2e9821c4db5037 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/dokpark21/DEX_Lending_solidity

   

# #1 올바르지 못한 initializer 제약
## 설명
src/DreamAcademyLending.sol : line No. 41 위치의 ```initializeLendingProtocol()```함수가 제약없이 여러번 호출될 수 있음.
initializer로 사용되는 ```initializeLendingProtocol()``` 함수에 별다른 호출제약이 존재하지 않음. 일반적으로 initializer는 한번만 호출되며, Owner에 의해 호출되도록 제약함. 아무나 호출할 수 있는 ```initializeLendingProtocol()```를 통해 usdc의 주소를 변경할 수 있는 것이 문제가 될 것으로 보임.


## 파급력 
```High ```   
공격자가 ```initializeLendingProtocol()```를 다시 호출하여 usdc토큰의 주소를 임의로 변경하고, 컨트랙트 내에 예치된 이더를 모두 탈취하거나 서비스를 사용할 수 없도록 만들 수 있음.

## 해결방안
```initializeLendingProtocol()``` 함수에 modifier를 이용해 Owner만 단 1번 호출할 수 있도록 변경함.


