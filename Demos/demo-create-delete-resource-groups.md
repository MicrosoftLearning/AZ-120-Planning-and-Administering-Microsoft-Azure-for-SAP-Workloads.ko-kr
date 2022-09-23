# <a name="demonstration-create-and-delete-resource-groups"></a>데모: 리소스 그룹 만들기 및 삭제

>**참고**: owner 및 사용자 액세스 관리자 역할만 리소스의 잠금을 관리할 수 있습니다.

## <a name="manage-resource-groups-in-the-portal"></a>포털에서 리소스 그룹 관리

1. Azure Portal에 액세스합니다.
1. Create a resource group. Remember the name of this resource group. 
1. 리소스 그룹의 **설정** 블레이드에서 **잠금**을 선택합니다.
1. To add a lock, select <bpt id="p1">**</bpt>Add<ept id="p1">**</ept>. If you want to create a lock at a parent level, select the parent. The currently selected resource inherits the lock from the parent. For example, you could lock the resource group to apply a lock to all its resources.
1. Give the lock a <bpt id="p1">**</bpt>name<ept id="p1">**</ept> and <bpt id="p2">**</bpt>lock type<ept id="p2">**</ept>. Optionally, you can add notes that describe the lock.
1. 잠금을 삭제하려면 사용 가능한 옵션에서 줄임표와 **삭제** 를 선택합니다.

## <a name="manage-resource-groups-with-powershell"></a>PowerShell을 사용하여 리소스 그룹 관리

1. Cloud Shell에 액세스합니다.
2. 리소스 잠금을 만들고 작업을 확인합니다.

    ```
        New-AzResourceLock -LockName <lockName> -LockLevel CanNotDelete -ResourceGroupName <resourceGroupName>
    ```

3. View resource lock information. Notice the LockId that will be used in the next step to delete the lock.

    ```
        Get-AzResourceLock
    ```

4. 리소스 잠금을 삭제하고 작업을 확인합니다. 

    ```
        Remove-AzResourceLock -LockName <Name> -ResourceGroupName <Resource Group>
    ```

5. 리소스 잠금이 제거되었는지 확인합니다.


    ```
        Get-AzResourceLock
    ```

>**참고:** 리소스 잠금을 구성하고, 리소스 그룹을 통해 리소스를 이동하고, 리소스 그룹을 제거하는 것은 인증 시험의 일부입니다.