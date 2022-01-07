# 데모: Active Directory 사용자 및 그룹 살펴보기

>**참고**: 구독에 따라 Active Directory 블레이드의 모든 영역을 사용할 수 있는 것은 아닙니다.

## 도메인 정보 결정

1. Azure Portal에 액세스하여 **Azure Active Directory** 블레이드로 이동합니다.
2. 사용 가능한 도메인 이름을 기록해 둡니다. usergmail.onmicrosoft.com을 예로 들 수 있습니다.

## 사용자 계정 살펴보기

1. **사용자** 블레이드를 선택합니다.
2. **새 사용자**를 선택합니다. 
3. **새 게스트 사용자** 만들기를 위한 선택을 확인합니다.
4. **새 사용자**를 만듭니다. 도메인을 바꿉니다. 

    + **이름**: *크리스 그린(Chris Green)*
    + **주소**: *chris@사용자 도메인*
    + **프로필 정보**: 이름과 성을 입력합니다. 
    + **디렉터리 역할** - *사용자*.

5. **사용자 설정** 블레이드를 검토합니다.
6. **감사 로그** 블레이드를 검토합니다.

## 그룹 계정을 살펴봅니다.

1. **그룹** 블레이드를 선택합니다.
2. **새 그룹**을 추가합니다. 

    + **그룹 유형**: *보안*
    + **그룹 이름**: *관리자*
    + **구성원 유형**: *할당됨*
    + **구성원**: 그룹에 *Chris Green*을 추가 

3. **설정**에서 **일반** 블레이드를 검토합니다.
4. **활동**에서 **감사 로그** 블레이드를 검토합니다.

## 그룹 관리를 위한 PowerShell을 살펴봅니다.

1. **Developers**라는 새 그룹을 만듭니다.

    ```
    New-AzADGroup -DisplayName Developers -MailNickname Developers
    ```

2. **Developers** 그룹 **ObjectId**를 검색합니다.

    ```
    Get-AzADGroup
    ```

3. 추가할 구성원의 사용자 **ObjectId**를 검색합니다.

    ```
    Get-AzADUser
    ```

4. 그룹에 사용자를 추가합니다. **groupObjectId**와 **userObjectId**를 바꿉니다.

    ```
    Add-AzADGroupMember -MemberUserPrincipalName ""myemail@domain.com"" -TargetGroupDisplayName ""MyGroupDisplayName""
    ```

5. 그룹의 구성원을 확인합니다. **groupObjectId**를 바꿉니다.

    ```
    Get-AzADGroupMember -GroupDisplayName "MyGroupDisplayName"
    ```
