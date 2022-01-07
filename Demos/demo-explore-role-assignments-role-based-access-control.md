# 데모: 역할 할당 및 역할 기반 액세스 제어 살펴보기

## 액세스 제어 블레이드 찾기

1. Azure Portal에 액세스하여 리소스 그룹을 선택합니다. 사용하는 리소스 그룹을 기록해둡니다. 
2. **IAM(액세스 제어)** 블레이드를 선택합니다. 
3. 이 블레이드는 액세스를 제어할 수 있도록 다양한 리소스에서 사용할 수 있습니다.

## 역할 권한 검토

1. **역할**탭(맨 위)을 선택합니다.
2. 사용 가능한 많은 기본 제공 역할을 검토합니다.
3. 역할을 더블 클릭한 다음 **권한**(맨 위)을 선택합니다.
4. 해당 역할에 대한 **읽기, 쓰기 및 삭제** 액선을 볼 수 있을 때까지 역할에 대한 드릴링을 계속합니다.
5. **IAM(액세스 제어)** 블레이드로 돌아갑니다.

## 역할 할당 추가

1. **역할 할당 추가**를 선택합니다. 

    + **역할**: *소유자*
    + **선택**: *관리자*
    + 변경 내용 **저장** 

2. **액세스 검사**를 선택합니다.
3. Chris Green **찾기**.
4. 관리자 그룹의 일부이며 소유자입니다. 
5. **할당을 거부**할 수 있습니다. 

## PowerShell 명령 살펴보기

1. Azure Cloud Shell을 엽니다.
2. PowerShell 드롭다운을 선택합니다.
3. 역할 정의를 나열합니다.

    ```PowerShell
    Get-AzRoleDefinition | FT Name, Description
    ```

4. 역할에 따른 작업을 나열합니다.

    ```PowerShell
    Get-AzRoleDefinition owner | FL Actions, NotActions
    ```

5. 역할 할당을 나열합니다.

    ```PowerShell
    Get-AzRoleAssignment -ResourceGroupName <resource group name>
    ```
