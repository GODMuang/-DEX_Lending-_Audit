
# Lending_Audit-sori
## 대상
|Name |Lending_solidity|
|--|--|
|Target/Version|Git Repository(Lending_solidity): commit 75e65dc5d5670cdb8894f75da9d6b38defb5c893 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/ExploitSori/Lending_solidity

   

# #1 올바르지 못한 deposit 처리
## 설명
src/UpsideAcademyLending.sol : line No. 67 
 ```deposit()```함수를 호출할 때, deposit한 금액이 추가되지 않고, 매번 새로이 deposit한 금액으로 대체됩니다.
 따라서 이전에 예금한 금액을 잃게됩니다.



## 파급력 
```Critical ```   
유저가 deposit할때마다 이전 예금액을 잃게되므로, 사용자에게 손해를 가져오게 됩니다.

## 해결방안
```deposit_list[msg.sender].usdc = amount;``` 코드를 ```deposit_list[msg.sender].usdc += amount;```로 수정한다.


