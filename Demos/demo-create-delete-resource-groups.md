---
ms.openlocfilehash: f925b00bee5e45d5636ec833a64601cfe8df7475
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857640"
---
# <a name="demonstration-create-and-delete-resource-groups"></a>데모: 리소스 그룹 만들기 및 삭제

>**참고**: owner 및 사용자 액세스 관리자 역할만 리소스의 잠금을 관리할 수 있습니다.

## <a name="manage-resource-groups-in-the-portal"></a>포털에서 리소스 그룹 관리

1. Azure Portal에 액세스합니다.
1. 리소스 그룹을 만듭니다. 이 리소스 그룹의 이름을 기억합니다. 
1. 리소스 그룹의 **설정** 블레이드에서 **잠금** 을 선택합니다.
1. 잠금을 추가하려면 **추가** 를 선택합니다. 부모 수준에서 잠금을 만들려면 부모를 선택합니다. 현재 선택된 리소스는 부모에서 잠금을 상속합니다. 예를 들어 리소스 그룹을 잠궈 모든 리소스에 잠금을 적용할 수 있습니다.
1. 잠금에 **이름** 과 **잠금 유형** 을 지정합니다. 필요한 경우 잠금을 설명하는 메모를 추가할 수 있습니다.
1. 잠금을 삭제하려면 사용 가능한 옵션에서 줄임표와 **삭제** 를 선택합니다.

## <a name="manage-resource-groups-with-powershell"></a>PowerShell을 사용하여 리소스 그룹 관리

1. Cloud Shell에 액세스합니다.
2. 리소스 잠금을 만들고 작업을 확인합니다.

    ```
        New-AzResourceLock -LockName <lockName> -LockLevel CanNotDelete -ResourceGroupName <resourceGroupName>
    ```

3. 리소스 잠금 정보를 확인합니다. 다음 단계에서 잠금을 삭제하는 데 사용할 LockId를 확인합니다.

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