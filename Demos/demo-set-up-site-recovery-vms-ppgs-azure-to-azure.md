# 데모: 근접 배치 그룹에서 가상 머신에 대한 Site Recovery 설정 - Azure 간

> **참고:** 이 기능은 현재 PowerShell을 통해 사용할 수 있으며 Managed Disks를 사용하는 모든 Azure VM을 지원합니다.

## 전제 조건

1. Azure PowerShell Az 모듈이 있는지 확인합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우, [Azure PowerShell을 설치 및 구성 가이드](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-5.0.0)를 따라합니다.
2. Azure PowerShell Az 버전은 최소  4.1.0이어야 합니다. 현재 버전을 확인하려면 다음 명령을 사용합니다.

    ```azurepowershell
	Get-InstalledModule -Name Az
    ```

>**참고:** 대상 근접 배치 그룹의 고유 ID가 있는지 확인합니다. 새 근접 배치 그룹을 만드는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#create-a-proximity-placement-group)에서 명령을 확인하고, 기존 근접 배치 그룹을 사용하는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#list-proximity-placement-groups)에서 명령을 사용합니다.

## Microsoft Azure 구독에 로그인

1. `Connect-AzAccount` cmdlet을 사용하여 Azure 구독에 로그인합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Azure 구독을 선택합니다. `Get-AzSubscription` cmdlet을 사용하여 액세스할 수 있는 Azure 구독 목록을 가져옵니다. `Set-AzContext` cmdlet을 사용하여 작업할 Azure 구독을 선택합니다.

    ```azurepowershell
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

## 복제할 가상 머신의 세부 정보 가져오기

1. 이 데모에서는 미국 동부 지역의 가상 머신이 미국 서부 2 지역에 복제되고 복구됩니다. 복제 중인 가상 머신에는 OS 디스크와 단일 데이터 디스크가 있습니다. 예제에서 사용되는 가상 머신의 이름은 `AzureDemoVM`입니다.

    ```azurepowershell
    # Get details of the virtual machine
    $VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

    Write-Output $VM
    ```

    ```Output
    ResourceGroupName  : A2AdemoRG
    Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
    VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
    Name               : AzureDemoVM
    Type               : Microsoft.Compute/virtualMachines
    Location           : eastus
    Tags               : {}
    DiagnosticsProfile : {BootDiagnostics}
    HardwareProfile    : {VmSize}
    NetworkProfile     : {NetworkInterfaces}
    OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState  : Succeeded
    StorageProfile     : {ImageReference, OsDisk, DataDisks}
    ```

1. 가상 머신의 디스크에 대한 디스크 세부 정보를 가져옵니다. 디스크 세부 정보는 나중에 가상 머신에 대한 복제를 시작할 때 사용됩니다.

    ```azurepowershell
    $OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
    $DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
    ```

## Recovery Services 자격 증명 모음 만들기

1. Recovery Services 자격 증명 모음을 만들 리소스 그룹을 만듭니다.

    > **중요:**
    > * Recovery Services 자격 증명 모음 및 보호 중인 가상 머신은 다른 Azure 위치에 있어야 합니다.
    > * Recovery Services 자격 증명 모음의 리소스 그룹과 보호 중인 가상 머신은 다른 Azure 위치에 있어야 합니다.
    > * Recovery Services 자격 증명 모음 및 해당 자격 증명 모음이 속한 리소스 그룹은 동일한 Azure 위치에 있을 수 있습니다.

1. 이 데모에서 보호되는 가상 머신은 미국 동부 지역에 있습니다. 재해 복구를 위해 선택한 복구 지역은 미국 서부 2 지역입니다. Recovery Services 자격 증명 모음과 자격 증명 모음의 리소스 그룹은 모두 복구 지역인 West US 2에 있습니다.

    ```azurepowershell
    #Create a resource group for the recovery services vault in the recovery Azure region
    New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
    ```

    ```Output
    ResourceGroupName : a2ademorecoveryrg
    Location          : westus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
    ```

1. Recovery Services 자격 증명 모음을 만듭니다. 이 예제에서는 `a2aDemoRecoveryVault`라는 Recovery Services 자격 증명 모음이 미국 서부 2 지역에서 만들어집니다.

    ```azurepowershell
    #Create a new Recovery services vault in the recovery region
    $vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

    Write-Output $vault
    ```

    ```Output
    Name              : a2aDemoRecoveryVault
    ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
    Type              : Microsoft.RecoveryServices/vaults
    Location          : westus2
    ResourceGroupName : a2ademorecoveryrg
    SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
    ```

## 자격 증명 모음 컨텍스트 설정

1. PowerShell 세션에서 사용할 자격 증명 모음 컨텍스트를 설정합니다. 자격 증명 모음 컨텍스트를 설정하면 PowerShell 세션의 Azure Site Recovery 작업이 선택한 자격 증명 모음의 컨텍스트에서 수행됩니다.

    ```azurepowershell
    #Setting the vault context.
    Set-AzRecoveryServicesAsrVaultContext -Vault $vault
    ```

    ```Output
    ResourceName         ResourceGroupName ResourceNamespace          ResourceType
    ------------         ----------------- -----------------          -----------
    a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
    ```

    ```azurepowershell
    #Delete the downloaded vault settings file
    Remove-Item -Path $Vaultsettingsfile.FilePath
    ```

1. Azure 갼 마이그레이션의 경우 자격 증명 모음 컨텍스트를 새로 만든 자격 증명 모음으로 설정할 수 있습니다.

    ```azurepowershell
    #Set the vault context for the PowerShell session.
    Set-AzRecoveryServicesAsrVaultContext -Vault $vault
    ```

## 자격 증명 모음을 준비하여 Azure 가상 머신 복제 시작

### 기본(원본) 지역을 나타내는 Site Recovery 패브릭 개체 만들기

자격 증명 모음의 패브릭 개체는 Azure 지역을 나타냅니다. 기본 패브릭 개체는 자격 증명 모음에 보호되는 가상 머신이 속한 Azure 지역을 나타내도록 작성됩니다. 이 데모에서 보호되는 가상 머신은 미국 동부 지역에 있습니다.

- 지역당 하나의 패브릭 개체만 만들 수 있습니다.
- Azure Portal에서 VM에 대한 Site Recovery 복제를 이전에 사용하도록 설정한 경우, Site Recovery가 패브릭 개체를 자동으로 만듭니다. 지역에 패브릭 개체가 있는 경우 새 개체를 만들 수 없습니다.

시작하기 전에 Site Recovery 작업이 비동기적으로 실행된다는 것을 파악합니다. 작업을 시작하면 Azure Site Recovery 작업이 제출되고 작업 추적 개체가 반환됩니다.

1. 작업 추적 개체를 사용하여 작업에 대한 최신 상태(`Get-AzRecoveryServicesAsrJob`)를 얻고 작업 상태를 모니터링합니다.

    ```azurepowershell
    #Create Primary ASR fabric
    $TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

    # Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            #If the job hasn't completed, sleep for 10 seconds before checking the job status again
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State

    $PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
    ```

1. 여러 Azure 지역의 가상 머신이 동일한 자격 증명 모음으로 보호되는 경우, 각 소스 Azure 지역에 대해 하나의 패브릭 개체를 만듭니다.

### 복구 지역을 나타내는 Site Recovery 패브릭 개체 만들기

복구 패브릭 개체는 복구 Azure 위치를 나타냅니다. 장애 조치가 있는 경우, 가상 머신이 복제되어 복구 패브릭으로 표시되는 복구 지역으로 복구됩니다. 이 예제에서 사용되는 복구 Azure 지역은 미국 서부 2입니다.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

## Site Recovery 보호 컨테이너 만들기

### 기본 패브릭에서 Site Recovery 보호 컨테이너 만들기

보호 컨테이너는 복제된 항목을 패브릭 내에서 그룹화하는 데 사용되는 컨테이너입니다.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### 복구 패브릭에서 Site Recovery 보호 컨테이너 만들기

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

## 복제 정책 만들기

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

## 보호 컨테이너 매핑 만들기

### 기본 컨테이너와 복구 보호 컨테이너 간에 보호 컨테이너 매핑 만들기

보호 컨테이너 매핑은 복구 보호 컨테이너 및 복제 정책을 통해 기본 보호 컨테이너를 매핑합니다. 보호 컨테이너 쌍 간에 가상 머신을 복제하는 데 사용할 각 복제 정책에 대해 하나의 매핑을 만듭니다.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### 장애 조치에 대한 보호 컨테이너 매핑 만들기 (장애 조치 후 역복제)

장애 조치 후, 장애 조치한 가상 머신을 원래 Azure 지역으로 다시 가져올 준비가 되면 장애 복구를 수행합니다. 장애 복구하려면, 장애 조치한 가상 머신이 장애 조치한 지역에서 원래 지역으로 역복제됩니다. 역복제의 경우 원래 지역의 역할과 복구 지역 스위치입니다. 원래 지역이 이제 새 복구 지역이 되고, 원래 복구 지역이 기본 지역이 되었습니다. 역복제를 위한 보호 컨테이너 매핑은 원래 지역 및 복구 지역의 전환된 역할을 나타냅니다.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## 캐시 스토리지 계정 및 대상 스토리지 계정 만들기

캐시 스토리지 계정은 복제 중인 가상 머신과 동일한 Azure 지역의 표준 스토리지 계정입니다. 캐시 스토리지 계정은 변경 내용이 복구 Azure 지역으로 이동하기 전에 복제 변경을 일시적으로 유지하는 데 사용됩니다. 필요하지는 않지만, 가상 머신의 다른 디스크에 대해 다른 캐시 스토리지 계정을 지정하도록 선택할 수 있습니다.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

**관리 디스크를 사용하지 않는** 가상 머신의 경우, 대상 스토리지 계정은 가상 머신의 디스크가 복제되는 복구 지역의 스토리지 계정입니다. 대상 스토리지 계정은 표준 스토리지 계정 또는 프리미엄 스토리지 계정일 수 있습니다. 디스크에 대한 데이터 변경 속도(IO 쓰기 속도)와 스토리지 유형에 대한 Azure Site Recovery 지원 이탈 제한을 기반으로 필요한 스토리지 계정 종류를 선택합니다.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## 네트워크 매핑 만들기

네트워크 매핑은 주 지역의 가상 네트워크를 복구 지역의 가상 네트워크에 매핑합니다. 네트워크 매핑은 기본 가상 네트워크의 가상 머신이 장애 조치해야하는 복구 지역의 Azure Virtual Network를 지정합니다. 하나의 Azure Virtual Network는 복구 지역의 단일 Azure Virtual Network에만 매핑할 수 있습니다.

1. 복구 지역에서 Azure Virtual Network를 만들어 다음으로 장애 조치합니다.

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

1. 기본 가상 네트워크를 검색합니다. 가상 머신이 연결된 VNet :

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

1. 기본 가상 네트워크와 복구 가상 네트워크 간의 네트워크 매핑 만들기:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

1. 역방향에 대한 네트워크 매핑 만들기(장애 복구(failback)):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## 관리 디스크를 사용하여 Azure Virtual Machine 복제

관리 디스크를 사용하여 Azure 가상 머신을 복제하려면 다음 PowerShell cmdlet을 사용합니다.

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    #Make sure to replace the variable $datadiskName with the data disk name.

    #Data disk
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $datadiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

    #Create a list of disk replication configuration objects for the disks of the virtual machine that will be replicated.

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

여러 데이터 디스크에서 복제를 사용하는 경우 다음 PowerShell cmdlet을 사용합니다.

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig

    #Data disk

    # Add data disks
    Foreach( $disk in $VM.StorageProfile.DataDisks)
    {
 	    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
        $dataDiskId1 = $datadisk[0].Id
        $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
        $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
        $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
             -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
             -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
        $diskconfigs += $DataDisk1ReplicationConfig
    }

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

근접 배치 그룹에서 영역 간 복제를 사용하는 경우 복제를 시작하는 명령이 다음 PowerShell cmdlet을 사용하여 교환됩니다.

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

복제 시작 작업이 성공하면 가상 머신 데이터가 복구 지역으로 복제됩니다.

복제 프로세스는 처음에 복구 영역에 있는 가상 머신의 복제 디스크의 복사본을 시드하면서 시작됩니다. 이 단계를 *초기 복제* 단계라고 합니다.

초기 복제가 완료되면 복제는 *차등 동기화* 단계로 이동합니다. 이 시점에서 가상 머신이 보호되고 해당 가상 머신에서 테스트 장애 조치(failover) 작업을 수행할 수 있습니다. 초기 복제가 완료된 후 가상 머신을 나타내는 복제된 항목의 복제 상태는 보호됨 상태로 변경됩니다.

해당하는 복제 보호된 항목의 세부 정보를 가져와서 복제 상태 및 가상 머신의 복제 상태를 모니터링합니다.

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

## 테스트 장애 조치(failover) 수행, 유효성 검사 및 정리

가상 머신에 대한 복제가 보호된 상태가 되면 테스트 장애 조치(failover) 작업을 가상 머신에서(가상 머신의 복제 보호된 항목에서) 수행할 수 있습니다

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

테스트 장애 조치(failover)를 수행합니다.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

테스트 장애 조치(failover) 작업이 완료될 때까지 기다립니다.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

테스트 장애 조치(failover) 작업이 완료되면 테스트 장애 조치(failover)된 가상 머신에 연결하려 테스트 장애 조치(failover)의 유효성을 검사할 수 있습니다.

테스트 장애 조치(failover)된 가상 머신에서 테스트가 완료되면 테스트 장애 조치(failover) 정리 작업을 시작하여 테스트 복사본을 정리합니다. 이 작업은 테스트 장애 조치(failover)로 만들어진 가상 머신의 테스트 복사본을 삭제합니다.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## 가상 머신 장애 조치(failover)

가상 머신을 특정 복구 지점으로 장애 조치(failover)합니다.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

장애 조치(failover) 작업이 성공하면 장애 조치(failover) 작업을 커밋할 수 있습니다.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## 원본 지역 다시 보호 및 장애 복구

원본 지역을 다시 보호하고 장애 복구하려면 다음 PowerShell cmdlet을 사용합니다.

```azurepowershell
#Create a cache storage account for replication logs in the primary region.
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage

#Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

## 복제 사용 안 함

`Remove-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet을 사용하여 복제를 사용하지 않도록 설정할 수 있습니다.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```