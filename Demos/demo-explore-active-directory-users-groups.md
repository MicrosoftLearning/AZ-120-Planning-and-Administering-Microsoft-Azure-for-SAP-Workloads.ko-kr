# <a name="demonstration-explore-actve-directory-users-and-groups"></a>데모: Active Directory 사용자 및 그룹 살펴보기

>**참고**: 구독에 따라 Active Directory 블레이드의 일부 영역을 사용하지 못할 수도 있습니다.

## <a name="determine-domain-information"></a>도메인 정보 결정

1. Azure Portal에 액세스하여 **Azure Active Directory** 블레이드로 이동합니다.
2. Make a note of your available domain name. For example, usergmail.onmicrosoft.com.

## <a name="explore-user-accounts"></a>사용자 계정 탐색

1. **사용자** 블레이드를 선택합니다.
2. **새 사용자**를 선택합니다. 
3. **새 게스트 사용자** 만들기를 위한 선택을 확인합니다.
4. Create a <bpt id="p1">**</bpt>New user<ept id="p1">**</ept>. Replace your domain. 

    + **이름**: *Chris Green*
    + **주소**: *chris@your 도메인*
    + **프로필 정보**: 이름과 성을 입력합니다. 
    + **디렉터리 역할** - *사용자*

5. **사용자 설정** 블레이드를 검토합니다.
6. **감사 로그** 블레이드를 검토합니다.

## <a name="explore-group-accounts"></a>그룹 계정을 살펴봅니다.

1. **그룹** 블레이드를 선택합니다.
2. **새 그룹**을 추가합니다. 

    + **그룹 유형**: *보안*
    + **그룹 이름**: *관리자*
    + **구성원 자격 유형**: *할당됨*
    + **멤버**: 그룹에 *Chris Green*을 추가 

3. **설정**에서 **일반** 블레이드를 검토합니다.
4. **활동**에서 **감사 로그** 블레이드를 검토합니다.

## <a name="explore-powershell-for-group-management"></a>그룹 관리를 위한 PowerShell을 살펴봅니다.

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

4. Add the user to the group. Replace <bpt id="p1">**</bpt>groupObjectId<ept id="p1">**</ept> and <bpt id="p2">**</bpt>userObjectId<ept id="p2">**</ept>.

    ```
    Add-AzADGroupMember -MemberUserPrincipalName ""myemail@domain.com"" -TargetGroupDisplayName ""MyGroupDisplayName""
    ```

5. Verify the members of the group. Replace <bpt id="p1">**</bpt>groupObjectId<ept id="p1">**</ept>.

    ```
    Get-AzADGroupMember -GroupDisplayName "MyGroupDisplayName"
    ```