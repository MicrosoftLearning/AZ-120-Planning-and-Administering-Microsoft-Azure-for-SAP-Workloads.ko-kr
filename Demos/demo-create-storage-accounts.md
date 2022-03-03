---
ms.openlocfilehash: 0106fd5b2837ad870b148f4ecebb4dcc690b89ca
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857661"
---
# <a name="demonstration-create-storage-accounts"></a>데모: 스토리지 계정 만들기

## <a name="create-a-storage-account-in-the-portal"></a>포털에서 스토리지 계정 만들기

1.  Azure Portal에서 **모든 서비스** 를 선택합니다. 리소스 목록에 스토리지 계정을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Storage 계정** 을 선택합니다.
2.  나타나는 Storage 계정 창에서 **추가** 를 선택합니다.
3.  스토리지 계정을 만들 **구독** 을 선택합니다.
4.  리소스 그룹 필드 아래에서 **새로 만들기** 를 선택합니다. 새 리소스 그룹의 이름을 입력합니다.
5.  스토리지 계정의 **이름** 을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
6.  스토리지 계정의 **위치** 를 선택하거나 기본 위치를 사용합니다.
7.  다음 필드는 기본값으로 유지합니다.

     * 배포 모델: **Resource Manager**
     * 성능: **표준**
     * 계정 종류: **StorageV2(범용 v2)**
     * 복제: **LRS(로컬 중복 스토리지)**
     * 액세스 계층: **핫**

8.  **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
9.  **만들기** 를 선택합니다.

## <a name="create-a-storage-account-using-powershell"></a>PowerShell을 사용하여 스토리지 계정 만들기

다음 코드를 사용하여 PowerShell을 통해 스토리지 계정을 만듭니다. 요구 사항에 맞게 저장소 유형과 이름을 바꾸세요.

```PowerShell
Get-AzLocation | select Location 
$location = "westus" 
$resourceGroup = "storage-demo-resource-group" 
New-AzResourceGroup -Name $resourceGroup -Location $location 
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "storagedemo" -Location $location -SkuName Standard_LRS -Kind StorageV2 
```

## <a name="create-a-storage-account-using-azure-cli"></a>Azure CLI를 사용하여 스토리지 계정 만들기

다음 코드를 사용하여 Azure CLI를 통해 스토리지 계정을 만듭니다. 요구 사항에 맞게 저장소 유형과 이름을 변경하세요.

```PowerShell
az group create --name storage-resource-group --location westus 
az account list-locations --query "[].{Region:name}" --out table 
az storage account create --name storagedemo --resource-group storage-resource-group --location westus --sku Standard_LRS --kind StorageV2 
```