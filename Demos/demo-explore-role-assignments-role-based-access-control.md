# <a name="demonstration-explore-role-assignments-and-role-based-access-control"></a>데모: 역할 할당 및 역할 기반 액세스 제어 탐색

## <a name="locate-access-control-blade"></a>액세스 제어 창 찾기

1. Azure Portal에 액세스하고 리소스 그룹을 선택합니다. 어떤 리소스 그룹을 사용하는지 메모합니다. 
2. **액세스 제어(IAM)** 창을 닫습니다. 
3. 이 창은 액세스를 제어할 수 있도록 다양한 리소스에 대해 사용할 수 있습니다.

## <a name="review-role-permissions"></a>역할 권한 검토

1. **역할** 탭(위쪽)을 선택합니다.
2. 사용할 수 있는 많은 기본 제공 역할을 검토합니다.
3. 역할을 두 번 클릭한 다음 **권한**(위쪽)을 선택합니다.
4. 해당 역할에 대한 **읽기, 쓰기 및 삭제** 작업을 볼 수 있을 때까지 계속해서 역할을 드릴다운할 수 있습니다.
5. **액세스 제어(IAM)** 창으로 돌아갑니다.

## <a name="add-a-role-assignment"></a>역할 할당 추가

1. **역할 할당 추가**를 선택합니다. 

    + **역할**: *소유자*
    + **Select**: *관리자*
    + 변경 내용을 **저장**합니다. 

2. **액세스 확인**을 선택합니다.
3. Chris Green **찾기**.
4. 관리자 그룹의 일부이며 소유자입니다. 
5. **할당을 거부**할 수 있습니다. 

## <a name="explore-powershell-commands"></a>PowerShell 명령 살펴보기

1. Azure Cloud Shell을 엽니다.
2. PowerShell 드롭다운을 선택합니다.
3. 역할 정의가 나열됩니다.

    ```PowerShell
    Get-AzRoleDefinition | FT Name, Description
    ```

4. 역할에 따른 작업을 나열합니다.

    ```PowerShell
    Get-AzRoleDefinition owner | FL Actions, NotActions
    ```

5. 역할 할당이 나열됩니다.

    ```PowerShell
    Get-AzRoleAssignment -ResourceGroupName <resource group name>
    ```