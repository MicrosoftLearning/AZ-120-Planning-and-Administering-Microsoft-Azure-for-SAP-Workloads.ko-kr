# <a name="demonstration-create-and-manage-file-shares-and-snapshots"></a>데모: 파일 공유 및 스냅샷을 만들고 관리

>**참고**: 이러한 단계에는 스토리지 계정이 필요합니다. 

## <a name="create-a-file-share-and-upload-a-file"></a>파일 공유 생성 및 파일 업로드

1. 스토리지 계정에 액세스하고 **파일**을 클릭합니다.
2. **+ 파일 공유**를 클릭하고 새 파일 공유에 **이름**과 **할당량**을 지정합니다.
3. 파일 공유를 만든 후 파일을 **업로드**합니다. 
4. **디렉터리 추가**, **공유 삭제** 및 **할당량** 편집 기능을 확인합니다.

## <a name="manage-snapshots"></a>스냅샷 관리

1. 파일 공유에 액세스합니다.
1. **스냅샷 만들기**를 선택합니다.
1. **스냅샷 보기**를 선택하고 스냅샷이 생성되었는지 확인합니다.
1. 스냅샷을 클릭하고 업로드한 파일이 포함되어 있는지 확인합니다.
1. 스냅샷의 일부인 파일을 클릭하고 **파일 속성**을 검토합니다. 
1. 스냅샷 파일을 **다운로드**하고 **복원**할 수 있습니다. 
1. 파일 공유에 액세스하고 이전에 업로드한 파일을 삭제합니다.
1. 스냅샷에서 파일을 **복원**합니다. 
 
## <a name="create-a-file-share-powershell"></a>파일 공유 만들기(PowerShell)

1. 스토리지 계정 및 키에 대한 컨텍스트를 만듭니다. 컨텍스트는 스토리지 계정 이름과 계정 키를 캡슐화합니다.

    ```PowerShell
    $storageContext = New-AzStorageContext storage-account-name storage-account-key
    ```

2. Create the file share. The name of your file share must be all lowercase.

    ```PowerShell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

## <a name="mount-a-file-share-powershell"></a>파일 공유 탑재(PowerShell)

1. Run the following commands from a regular (i.e. not an elevated) PowerShell session to mount the Azure file share. Remember to replace <bpt id="p1">**</bpt>your-resource-group-name<ept id="p1">**</ept>, <bpt id="p2">**</bpt>your-storage-account-name<ept id="p2">**</ept>, <bpt id="p3">**</bpt>your-file-share-name<ept id="p3">**</ept>, and <bpt id="p4">**</bpt>desired-drive-letter<ept id="p4">**</ept> with the proper information.

    ```PowerShell
    $resourceGroupName = "your-resource-group-name"
    $storageAccountName = "your-storage-account-name"
    $fileShareName = "your-file-share-name"

    # These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    $storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
    $fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
        $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
    }

    if ($fileShare -eq $null) {
        throw [System.Exception]::new("Azure file share not found")
    }

    # The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
    # storage-account.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
    # used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
    # hosts for Azure file shares (and other storage resources).
    $password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
    New-PSDrive -Name desired-drive-letter -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
    ```

2. 완료되면 다음 명령을 실행하여 파일 공유를 탑재 해제할 수 있습니다.

    ```PowerShell
    Remove-PSDrive -Name desired-drive-letter
    ```