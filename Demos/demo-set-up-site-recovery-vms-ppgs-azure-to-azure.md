# <a name="demonstration-set-up-site-recovery-for-virtual-machines-in-proximity-placement-groups---azure-to-azure"></a>데모: 근접 배치 그룹에서 가상 머신에 대한 Site Recovery 설정 - Azure 간

> **참고:** 이 기능은 현재 PowerShell을 통해 사용할 수 있으며 Managed Disks를 사용하는 모든 Azure VM을 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure PowerShell Az 모듈이 있어야 합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-5.0.0)을 참조하세요.
2. 최소 Azure PowerShell Az 버전은 4.1.0이어야 합니다. 현재 버전을 확인하려면 다음 명령을 사용합니다.

    ```azurepowershell
    Get-InstalledModule -Name Az
    ```

>**참고:** 대상 근접 배치 그룹의 고유 ID가 있는지 확인합니다. 새 근접 배치 그룹을 만드는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#create-a-proximity-placement-group)에서 명령을 확인하고 기존 근접 배치 그룹을 사용하는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#list-proximity-placement-groups)에서 명령을 사용합니다.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

1. `Connect-AzAccount` cmdlet을 사용하여 Azure 구독에 로그인합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Azure 구독을 선택합니다. `Get-AzSubscription` cmdlet을 사용하여 액세스 권한이 있는 Azure 구독 목록을 가져옵니다. `Set-AzContext` cmdlet을 사용하여 작업에 사용할 Azure 구독을 선택합니다.

    ```azurepowershell
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>복제할 가상 머신의 세부 정보 가져오기

1. 이 데모에서는 미국 동부 지역의 가상 머신이 미국 서부 2 지역에 복제되고 복구됩니다. 복제되는 가상 머신에는 OS 디스크와 단일 데이터 디스크가 있습니다. 이 예제에서 사용되는 가상 머신의 이름은 `AzureDemoVM`입니다.

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

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. Recovery Services 자격 증명 모음을 만들 리소스 그룹을 만듭니다.

    > **중요:**
    > * Recovery Services 자격 증명 모음 및 보호 중인 가상 머신은 다른 Azure 위치에 있어야 합니다.
    > * Recovery Services 자격 증명 모음의 리소스 그룹과 보호 중인 가상 머신은 다른 Azure 위치에 있어야 합니다.
    > * Recovery Services 자격 증명 모음 및 이에 속한 리소스 그룹은 동일한 Azure 위치에 있을 수 있습니다.

1. 이 데모에서 보호되는 가상 머신은 미국 동부 지역에 있습니다. 재해 복구를 위해 선택한 복구 지역은 미국 서부 2 지역입니다. Recovery Services 자격 증명 모음과 자격 증명 모음의 리소스 그룹은 둘 다 복구 지역인 미국 서부 2에 있습니다.

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

1. Recovery Services 자격 증명 모음을 만듭니다. 이 예제에서는 `a2aDemoRecoveryVault`라는 Recovery Services 자격 증명 모음이 미국 서부 2 지역에 생성됩니다.

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

## <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

1. PowerShell 세션에 사용할 자격 증명 모음 컨텍스트를 설정합니다. 자격 증명 모음 컨텍스트를 설정한 후에는 PowerShell 세션의 Azure Site Recovery 작업이 선택한 자격 증명 모음의 컨텍스트에서 수행됩니다.

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

1. Azure 간 마이그레이션의 경우 자격 증명 모음 컨텍스트를 새로 만든 자격 증명 모음으로 설정할 수 있습니다.

    ```azurepowershell
    #Set the vault context for the PowerShell session.
    Set-AzRecoveryServicesAsrVaultContext -Vault $vault
    ```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Azure Virtual Machines 복제를 시작하려면 자격 증명 모음을 준비합니다.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>기본(원본) 지역을 나타내는 Site Recovery 패브릭 개체 만들기

자격 증명 모음에 있는 패브릭 개체는 Azure 지역을 나타냅니다. 기본 패브릭 개체는 자격 증명 모음에 보호 중인 가상 머신이 속한 Azure 지역을 나타내기 위해 작성됩니다. 이 데모에서 보호되는 가상 머신은 미국 동부 지역에 있습니다.

- 패브릭 개체는 지역당 하나만 만들 수 있습니다.
- Azure Portal에서 VM에 대한 Site Recovery 복제를 이전에 사용하도록 설정한 경우, Site Recovery가 패브릭 개체를 자동으로 만듭니다. 특정 지역의 패브릭 개체가 있으면 새 개체를 만들 수 없습니다.

시작하기 전에 Site Recovery 작업이 비동기적으로 실행된다는 것을 파악합니다. 작업을 시작하면 Azure Site Recovery 작업이 제출되고 작업 추적 개체가 반환됩니다.

1. 작업 추적 개체를 사용하여 작업(`Get-AzRecoveryServicesAsrJob`)의 최신 상태를 가져오고 작업 상태를 모니터링할 수 있습니다.

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

1. 여러 Azure 지역의 가상 머신이 동일한 자격 증명 모음에 대해 보호되는 경우 각 원본 Azure 지역에 대한 하나의 패브릭 개체를 만듭니다.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>복구 지역을 나타내는 Site Recovery 패브릭 개체 만들기

복구 패브릭 개체는 복구 Azure 위치를 나타냅니다. 장애 조치가 있는 경우 가상 머신이 복제되고 복구 패브릭에 표시된 복구 지역으로 복구됩니다. 이 예에서 사용된 복구 Azure 지역은 미국 서부 2입니다.

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

## <a name="create-site-recovery-protection-containers"></a>Site Recovery 보호 컨테이너 만들기

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>기본 패브릭에서 Site Recovery 보호 컨테이너 만들기

보호 컨테이너는 패브릭 내에서 복제된 항목을 그룹화하는 데 사용하는 컨테이너입니다.

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

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>복구 패브릭에서 Site Recovery 보호 컨테이너 만들기

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

## <a name="create-a-replication-policy"></a>복제 정책 만들기

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

## <a name="create-protection-container-mappings"></a>보호 컨테이너 매핑 만들기

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>기본 및 복구 보호 컨테이너 간의 보호 컨테이너 매핑을 만듭니다.

보호 컨테이너 매핑은 복구 보호 컨테이너 및 복제 정책을 사용하여 기본 보호 컨테이너를 매핑합니다. 보호 컨테이너 쌍 간에 가상 머신을 복제하는 데 사용할 각 복제 정책에 대한 매핑을 하나 만듭니다.

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

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>장애 복구(failback)에 대한 보호 컨테이너 매핑을 만듭니다(장애 조치(failover) 후 역방향 복제).

장애 조치 후, 장애 조치된 가상 머신을 원래 Azure 지역으로 다시 되돌릴 준비가 되면 장애 복구(failback)를 수행합니다. 장애 복구를 위해 장애 조치된 가상 머신이 장애 조치된 지역에서 원래 지역으로 역방향 복제됩니다. 역방향 복제의 경우 원래 지역과 복구 지역의 역할이 바뀝니다. 이제 원래 지역이 새 복구 지역이 되고 원래 복구 지역이었던 곳이 기본 지역이 됩니다. 역방향 복제에 대한 보호 컨테이너 매핑은 원래 지역과 복구 지역의 전환된 역할을 나타냅니다.

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

## <a name="create-cache-storage-account-and-target-storage-account"></a>캐시 스토리지 계정 및 대상 스토리지 계정 만들기

캐시 스토리지 계정은 복제되는 가상 머신과 같은 Azure 지역에 있는 표준 스토리지 계정입니다. 캐시 스토리지 계정은 변경 내용이 복구 Azure 지역으로 이동하기 전에 복제 변경을 일시적으로 유지하는 데 사용됩니다. 가상 머신의 디스크마다 다른 캐시 스토리지 계정을 지정할 수 있지만 필수는 아닙니다.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

**관리 디스크를 사용하지 않는** 가상 머신의 경우 대상 스토리지 계정은 가상 머신의 디스크가 복제되는 복구 지역에 있는 스토리지 계정입니다. 대상 스토리지 계정은 표준 스토리지 계정 또는 Premium Storage 계정 중 하나일 수 있습니다. 디스크의 데이터 변경률(IO 쓰기 속도)과 Azure Site Recovery에서 스토리지 유형에 대해 지원하는 변동 한도에 따라 필요한 스토리지 계정 종류를 선택합니다.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>네트워크 매핑 만들기

네트워크 매핑은 기본 지역의 가상 네트워크를 복구 지역의 가상 네트워크에 연결합니다. 네트워크 매핑은 주 가상 네트워크의 가상 머신이 장애 조치되어야 하는 복구 지역의 Azure Virtual Network를 지정합니다. 하나의 Azure Virtual Network는 복구 지역의 단일 Azure Virtual Network에만 매핑할 수 있습니다.

1. 장애 조치할 복구 지역에서 Azure Virtual Network를 만듭니다.

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

1. 주 가상 네트워크를 검색합니다. 가상 머신이 연결된 VNet입니다.

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

1. 주 가상 네트워크와 복구 가상 네트워크 간에 네트워크 매핑을 만듭니다.

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

1. 장애 복구(failback)를 위한 역방향 네트워크 매핑을 만듭니다.

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

## <a name="replicate-an-azure-virtual-machine-with-managed-disks"></a>관리 디스크를 사용하여 Azure Virtual Machine 복제

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

    When you're enabling replication for multiple data disks, use the following PowerShell cmdlet:

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

    When you're enabling zone-to-zone replication with a proximity placement group, the command to start replication will be exchanged with the PowerShell cmdlet:

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

    After the operation to start replication succeeds, virtual machine data is replicated to the recovery region.

    The replication process starts by initially seeding a copy of the replicating disks of the virtual machine in the recovery region. This phase is called the *initial replication* phase.

    After initial replication finishes, replication moves to the *differential synchronization* phase. At this point, the virtual machine is protected, and you can perform a test failover operation on it. The replication state of the replicated item that represents the virtual machine goes to the protected state after initial replication finishes.

    Monitor the replication state and replication health for the virtual machine by getting details of the replication protected item that corresponds to it:

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

## <a name="perform-validate-and-clean-up-a-test-failover"></a>테스트 장애 조치(failover) 수행, 유효성 검사 및 정리

가상 머신 복제가 보호된 상태로 전환되면 가상 머신(가상 머신의 복제 보호된 항목)에서 테스트 장애 조치 작업을 수행할 수 있습니다.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

테스트 장애 조치를 수행합니다.

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

테스트 장애 조치 작업이 성공적으로 완료되면 테스트 장애 조치된 가상 머신에 연결하고 테스트 장애 조치의 유효성을 검사할 수 있습니다.

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

## <a name="fail-over-the-virtual-machine"></a>가상 머신 장애 조치(failover)

가상 머신을 특정 복구 지점으로 장애 조치합니다.

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

장애 조치 작업이 성공하면 장애 조치 작업을 커밋할 수 있습니다.

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

## <a name="reprotect-and-fail-back-to-the-source-region"></a>다시 보호 및 원본 지역으로 장애 복구(failback)

원본 지역을 다시 보호하고 장애 복구하려면 다음 PowerShell cmdlet을 사용합니다.

```azurepowershell
#Create a cache storage account for replication logs in the primary region.
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage

#Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

## <a name="disable-replication"></a>복제 사용 안 함

`Remove-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet을 사용하여 복제를 사용하지 않도록 설정할 수 있습니다.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```