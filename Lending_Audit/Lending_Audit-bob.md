
# Lending_Audit-bob
## 대상
|Name |Lending_solidity|
|--|--|
|Target/Version|Git Repository(Lending_solidity): commit 175e80d51b44fa98538c24a1ad0121f43b7ab7c4 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/choihs0457/Lending_solidity

   

# #1 올바르지 못한 토큰검증 -1
## 설명
src/Lending.sol : line No. 103 위치의 ```deposit()```호출 시, 토큰종류에 대한 검증이 존재하지 않음.
따라서 usdc가 아닌 다른 종류의 토큰도 예치가 가능함.



## 파급력 
```Informational ```   
사용자가 거래소에서 취급하지 않는 타입의 토큰을 이더와 함께 실수로 이체했을 시에 제대로 돌려받을 수 없음. 

## 해결방안
```initializeLendingProtocol()``` 함수에 modifier를 이용해 Owner만 단 1번 호출할 수 있도록 변경함.



---   
<br>
<br>
<br>


# #1 올바르지 못한 토큰검증 -2
## 설명
src/Lending.sol : line No. 156 위치의 ```repay()```호출 시, 토큰종류에 대한 검증이 존재하지 않음.
따라서 usdc가 아닌 다른 종류의 토큰으로 repay가 가능하게 됨.



## 파급력 
```Critical ```   
공격자가 자금을 빌리고, USDC보다 더 가치가 낮은 토큰을 이용해서 ```repay()```를 호출하여 컨트랙트 내 자금을 모두 탈취할 수 있음.

## 해결방안
함수호출 전, 토큰의 종류를 확인한다.


