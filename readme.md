1. npm init -y
2. npm install

## Step 1

Connecting to blockchain and accounts:

1. 리믹스 : ttps://remix.ethereum.org/
2. 메타마스크 연결
3. 오너 계정 연결
4. 거래를 위해 ETH를 보낼 소유자 계정

## Step 2

리믹스 "Injected Web3"으로 설정
Remix의 "계정" 섹션에서 소유자의 계정이 선택되어 있는지 확인
선택한 ERC20 컴파일

중요: ERC20 계약이 소수점 이하 18자리이고 ERC20 표준을 완전히 준수하는지 확인하십시오. 이것은 이 토큰이 이더리움 메인넷의 DEX에서 거래될 수 있도록 하는 요구사항이 있음

ERC20 스마트컨트랙트를 배포 그후. Remix의 "contract" 드롭다운 섹션이 "YourERC20Contract.sol"로 설정되어 있는지 확인(드롭다운은 상속된 다른 계약으로 재설정되므로 매번 배포할 올바른 계약을 수동/의도적으로 선택해야 함).

!!주의 : 배포가 완료되면 향후 사용을 위해 ERC20 계약의 주소와 tx 해시를 기록해야함

## Step 3

Remix의 "환경"이 "Injected Web3"으로 설정되어 있고 Remix의 "계정" 섹션에서 소유자의 계정이 선택되어 있는지 확인

TimeRock 스마트 컨트랙트 컴파일
Timelock 계약을 배포합니다. Remix의 "contract" 드롭다운 섹션이 "simpleTImeRock.sol"로 설정되어 있는지 확인.

생성자의 유일한 매개변수로 ERC20 토큰의 주소를 입력해야함.

## Step 4

`setTimestamp` 함수를 호출하여 계약의 타임스탬프를 설정합니다. `_timePeriodInSeconds`라는 함수 매개변수는 하나만 있습니다. 시간 잠금 기간을 유지하려는 시간(초)을 반영합니다.

`timestampSet`

timestampSet 변수가 `true`인지 확인하십시오.

https://user-images.githubusercontent.com/9831342/150715021-63881e4f-02d8-43d5-a421-452ce2155461.png

`initialTimestamp`

`initialTimestamp` 변수를 확인하고 온라인 epoch to date 변환기를 사용하여 적절한 날짜 형식으로 값을 확인합니다.

https://user-images.githubusercontent.com/9831342/150715109-8ef231f7-45f3-48a8-8c1a-25bc9b561a66.png

예를 들어 위 이미지의 값 1642993044는 2022년 1월 24일 월요일 12:57:24 GMT+10:00에 해당합니다.

## Step 5

관련 ERC20 계약을 확인합니다.

우선, 이 타임락 계약과 관련된 ERC20 계약이 올바른지 절대적으로 확인해야합니다. 이는 아래와 같이 erc20Contract 변수를 호출하여 수행할 수 있습니다.

https://user-images.githubusercontent.com/9831342/150715931-edfe5dde-1c6c-4651-8239-c6cb01b27971.png

## Step 6

사용자 토큰을 타임락 계약에 할당합니다.

토큰은 아래와 같이 `depositToken` 기능을 사용하여 한 번에 하나씩 사용자에게 할당할 수 있습니다.
https://user-images.githubusercontent.com/9831342/150716076-6d6bed3b-25ac-48ce-b54a-f7d7a3e94b26.png

토큰은 `bulkDepositTokens` 기능을 사용하여 대량으로 할당할 수도 있습니다. 이것은 타사 스크립트를 사용하여 수행함. 이 스크립트에 액세스하려면 문제가 생기면 https://github.com/second-state/simple-timelock-smart-contract/blob/main/README.md#:~:text=a%20third%2Dparty%20script (현재 없어짐..) 문의해야합니다 .

사용자에게 할당한 토큰(모든 ERC20 토큰의 합계)의 정확한 기록을 항상 유지해야합니다. 이 합계 수치는 다음 단계에 필요합니다(그리고 잠금 해제를 수행할 모든 사용자에게 서비스를 제공하기 위해 타임락 계약에 정확한 양의 토큰이 있는지 확인합니다).

## Step 7

ERC20 토큰을 타임락 계약으로 전송합니다.

ERC20 토큰 계약에서 전송 기능을 사용하여 ERC20 토큰을 타임록 스마트 계약의 주소로 전송하십시오.

https://user-images.githubusercontent.com/9831342/150716642-b2d63734-e928-4513-aa44-860e016be358.png

아래와 같이 ERC20 컨트랙트의 `balanceOf` 함수에 타임락 컨트랙트의 주소를 붙여넣어 해당 토큰이 전송되었음을 확인할 수 있습니다.

https://user-images.githubusercontent.com/9831342/150716797-ec250368-538d-4105-80c2-8427031b57c6.png

## Step 8

소유자가 참여한후

모든 할당이 이루어지고 ERC20 토큰이 타임락 계약으로 전송되면 소유자는 타임락 계약의 `finalizeAllIncomingDeposits()` 함수를 호출할 수 있습니다. 이것은 시간 잠금을 비수탁으로 만들고 계약 소유자는 토큰 금액 등을 변경할 수 없습니다. TimeRock 계약의 작동은 순전히 이 시점부터 `transferTimeLockedTokensAfterTimePeriod` 함수를 기반으로 합니다.

## Step 9

이 간단한 TimeRock 스마트컨트랙트는 오픈소스 Front End 사용자 인터페이스가 있습니다.
개방형 웹에 배포하려면 거기에 있는 지침을 따르십시오.
