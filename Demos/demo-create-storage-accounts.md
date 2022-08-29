# <a name="demonstration-create-storage-accounts"></a>데모: 스토리지 계정 만들기

## <a name="create-a-storage-account-in-the-portal"></a>포털에서 스토리지 계정 만들기

1.  In the Azure portal, select <bpt id="p1">**</bpt>All services<ept id="p1">**</ept>. In the list of resources, type Storage Accounts. As you begin typing, the list filters based on your input. Select <bpt id="p1">**</bpt>Storage Accounts<ept id="p1">**</ept>.
2.  나타나는 Storage 계정 창에서 **추가**를 선택합니다.
3.  스토리지 계정을 만들 **구독**을 선택합니다.
4.  Under the Resource group field, select <bpt id="p1">**</bpt>Create new<ept id="p1">**</ept>. Enter a name for your new resource group.
5.  Enter a <bpt id="p1">**</bpt>name<ept id="p1">**</ept> for your storage account. The name you choose must be unique across Azure. The name also must be between 3 and 24 characters in length, and can include numbers and lowercase letters only.
6.  스토리지 계정의 **위치**를 선택하거나 기본 위치를 사용합니다.
7.  다음 필드는 기본값으로 유지합니다.

     * 배포 모델: **Resource Manager**
     * 성능: **표준**
     * 계정 종류: **StorageV2(범용 v2)**
     * 복제: **LRS(로컬 중복 스토리지)**
     * 액세스 계층: **핫**

8.  **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
9.  **만들기**를 선택합니다.

## <a name="create-a-storage-account-using-powershell"></a>PowerShell을 사용하여 스토리지 계정 만들기

Azure Portal에서 **모든 서비스**를 선택합니다.

```PowerShell
Get-AzLocation | select Location 
$location = "westus" 
$resourceGroup = "storage-demo-resource-group" 
New-AzResourceGroup -Name $resourceGroup -Location $location 
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "storagedemo" -Location $location -SkuName Standard_LRS -Kind StorageV2 
```

## <a name="create-a-storage-account-using-azure-cli"></a>Azure CLI를 사용하여 스토리지 계정 만들기

리소스 목록에 스토리지 계정을 입력합니다.

```PowerShell
az group create --name storage-resource-group --location westus 
az account list-locations --query "[].{Region:name}" --out table 
az storage account create --name storagedemo --resource-group storage-resource-group --location westus --sku Standard_LRS --kind StorageV2 
```