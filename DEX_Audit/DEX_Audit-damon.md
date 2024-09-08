
# DEX_Audit-damon
## 대상
|Name |DEX_solidity|
|--|--|
|Target/Version|Git Repository(DEX_solidity): commit 1671f2f3a6a272387b4a884e92f9724563d9cb15 (master branch)|
|Application Type | Smart Contract|
|Lang / Platform | Smart contracts [Solidity] |
|link | https://github.com/gloomydumber/DEX_solidity

   

# #1 올바르지 못한 비율의 유동성 추가
## 설명
src/Dex.sol : line No. 54 위치의 ```addLiquidity()```이 호출되어 유동성이 추가될 때, 현재 유동성 풀과 다른 비율의 유동성을 집어넣으면 문제가 발생한다. 코드에서는 ```liquidity``` 변수를 이용해 추가된 유동성중에 더 적은 양의 토큰을 기준으로 lp 토큰을 발행하여 풀 내의 비율을 유지하고자 하지만, 풀에 추가되고 남은 토큰을 다시 사용자에게 돌려주지 않음에 있어서 문제가 발생할 것으로 보인다.


## 파급력 
```Low ```   
lp를 발행하고 남은 토큰을 돌려주지 않게되면서 ```addLiquidity()```를 오용한 사용자 본인에게 손해를 끼치는 버그입니다. 그와 더불어 남은 토큰이 그대로 풀에 추가되게 되면서, 실제 토큰 비율에도 영향을 미쳐 서비스에 영향을 주게됩니다.

## 해결방안
토큰을 풀에 추가하기 전 또는 후에 사용자에게 남은 토큰을 돌려준다.

---   
<br>
<br>
<br>




# #2 유동성 제거 시 올바르지 못한 호출로 인한 토큰반환 누락
## 설명
src/Dex.sol : line No. 80 위치의 ```removeLiquidity()``` 가 호출되어 lp토큰을 반환하고 tokenX와tokenY를 비율에 맞게 반환받아야 하나, lp토큰만 수거할 뿐, tokenX와 tokenY를 전혀 반환하지 않는다. ```burn()```함수의 visibility 설정미흡으로 발생한 문제로 보인다.   
 

```removeLiquidity()```를 호출할 때 컨트랙트 내에서 선언된 ```burn()```을 호출하게 되는데, burn 함수에서는 ```safeTransfer()```를 이용해서 ```msg.sender```에게 tokenX와 tokenY를 반환하게 구성되어 있다. 하지만 ```burn()```함수는 ```external```로 선언되어있어 ```this.burn()```과 같은 형식으로만 호출 할 수 있고, 이와 같은 형식으로 호출하게된다면 msg.sender는 사용자가 아닌 현재 DEX 컨트랙트의 주소로 변환된다. 따라서 사용자가 아닌 자신에게```safeTransfer()```를 호출하게 되면서 컨트랙트 스스로에게 토큰을 돌려줄 뿐, 사용자는 아무런 토큰도 돌려받지 못하게 된다.



## 파급력 
```Critical ```   
lp토큰을 수거할 뿐 원래 유동성으로 제공했던 tokenX와 tokenY를 전혀 반환하지 않는다는 점에서 볼 때, lp토큰의 가치를 무의미하게 만들고 해당 DEX의 이용가치를 사라지게 한다는 점에서 치명적인 오류로 보여진다. 


## 해결방안
```burn()``` 함수의 visibility를 external에서 internal로 변경한다. 그리고 ```removeLiquidity()```에서 ```this.burn()```을 ```burn()```으로 변경하여 내부호출할 수 있도록 한다.


---   
<br>
<br>
<br>




# #3 올바르지 못한 visibility-1
## 설명
src/Dex.sol : line No. 138 위치의 ```update()``` 의 visibility가 ```public```으로 설정되어 있어서 ```reserveX```와 ```reserveY``` 변수를 임의로 변경 가능하다.
여기서 조작 가능한 ```reserveX```와 ```reserveY``` 변수는 전역변수로, ```mint()```와 ```swap()```시에 사용되어 결과에 영향을 미칠 수 있다. 하지만 ```update()```함수는 추가적인 호출자 검증이 존재하지 않은 상태에서 ```public```으로 선언되어 있어서 문제가 발생한다.



## 파급력 
```Critical ```   
```update()```를 공격자가 마음대로 호출할 수 있게되면서 공격자는 lp토큰을 마음대로 발행하여 거래소의 토큰을 모두 탈취할 수도 있다. 
테스트 예로 유동성 풀에 ```dex.update(1, 1)```을 호출하여 reserve 변수의 값을 매우 작게만든 뒤, 다시 약간의 유동성을 추가하는것으로 2000000000000000000000000 eth가량 lp토큰을 생성해내는 케이스를 확인할 수 있었다. 이렇게 발행된 lp로 공격자는 거래소 내 토큰을 모두 환전해갈 수 있었으며 치명적인 파급력을 가져올 것으로 보인다.


## 해결방안
```update()``` 함수의 visibility를 public에서 internal로 변경한다. 



---   
<br>
<br>
<br>




# #4 올바르지 못한 visibility-2
## 설명
src/Dex.sol : line No. 94 위치의 ```mint()``` 의 visibility가 external로 설정되어 있음. 이에 따라 사용자는 ```mint()```를 독자적으로 호출할 수 있게됨.



## 파급력 
```Low ```   
```mint()```를 단독 호출했을 때 mint될 유동성을 따로 계산하면서 악용가능성은 보이지 않으나, 다른 취약점들과 결합하여 악용할 수 있을 것으로 보임.

## 해결방안
```mint()``` 함수의 visibility를 external에서 internal로 변경한다. 그리고 ```addLiquidity()```에서 ```this.mint()```을 ```mint()```으로 변경하여 내부호출할 수 있도록 한다.


---   
<br>
<br>
<br>




# #5 올바르지 못한 visibility-3
## 설명
src/Dex.sol : line No. 150 위치의 ```transferFrom()``` 의 visibility가 external로 설정되어 있음. ```transferFrom()```에서는 ```transfer()```가 아닌 ```_transfer()```을 직접 호출하기때문에 from부분을 임의로 조작할 수 있음. 더불어 to 의 주소가 해당 거래소일 경우에는 추가적인 호출검증 과정을 거치지 않기때문에 강제로 타 사용자의 토큰을 반환시킬 수 있게됨.





## 파급력 
```Medium ```   
 ```transferFrom()```에서 to 의 주소가 거래소가 아닐 경우에는 allowance를 확인하여 타 사용자에게 전송하는 부분은 방지할 수 있지만, to의 주소가 거래소일 경우에는 타 사용자의 lp를 강제로 거래소에 반환시킬 수 있게됨. 이에 따라 공격자는 ```transferFrom()```를 독자적으로 호출하여 거래소에 타 사용자의 lp토큰을 강제로 반환시킬 수 있음.

## 해결방안
```transferFrom()``` 함수의 visibility를 external에서 internal로 변경한다.
