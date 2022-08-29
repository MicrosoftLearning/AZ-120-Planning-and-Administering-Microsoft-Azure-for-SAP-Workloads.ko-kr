# <a name="demonstration-explore-azcopy"></a>데모: AzCopy 살펴보기

## <a name="download-azcopy"></a>AzCopy 다운로드

First, download the AzCopy V10 executable file to any directory on your computer. AzCopy V10 is just an executable file, so there's nothing to install.

- [Windows 64비트](https://aka.ms/downloadazcopy-v10-windows)(zip)
- [Windows 32비트](https://aka.ms/downloadazcopy-v10-windows-32bit)(zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

These files are compressed as a zip file (Windows and Mac) or a tar file (Linux). To download and decompress the tar file on Linux, see the documentation for your Linux distribution.

## <a name="run-azcopy"></a>AzCopy 실행

For convenience, consider adding the directory location of the AzCopy executable to your system path for ease of use. That way you can type <ph id="ph1">`azcopy`</ph> from any directory on your system.

AzCopy 디렉터리를 경로에 추가하지 않도록 선택하는 경우 디렉터리를 AzCopy 실행 파일의 위치로 변경하고 Windows PowerShell 명령 프롬프트에서 `azcopy` 또는 `.\azcopy`를 입력해야 합니다.

먼저 AzCopy V10 실행 파일을 컴퓨터의 아무 디렉터리에나 다운로드합니다. 

## <a name="authorize-azcopy"></a>AzCopy 권한 부여

AD(Azure Active Directory) 또는 SAS(공유 액세스 서명) 토큰을 사용하여 권한 부여 자격 증명을 제공할 수 있습니다.

이 표를 가이드로 참조하세요.

| 스토리지 유형 | 현재 지원되는 권한 부여 방법 |
|--|--|
|**Blob Storage** | Azure AD 및 SAS |
|**Blob Storage(계층 구조 네임스페이스)** | Azure AD 및 SAS |
|**File Storage** | SAS 전용 |

### <a name="option-1-use-azure-active-directory"></a>옵션 1: Azure Active Directory 사용

AzCopy V10은 실행 파일일 뿐 이므로 아무것도 설치할 것이 없습니다.  

> <bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> In the current release, if you plan to copy blobs between storage accounts, you'll have to append a SAS token to each source URL. You can omit the SAS token only from the destination URL. For examples, see <bpt id="p1">[</bpt>Copy blobs between storage accounts<ept id="p1">](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-data)</ept>.

Azure AD를 사용하여 액세스 권한을 부여하는 방법은 [AzCopy 및 Azure AD(Azure Active Directory)를 사용하여 Blob에 대한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-authorize-azure-active-directory)를 참조하세요.

### <a name="option-2-use-a-sas-token"></a>옵션 2: SAS 토큰 사용

AzCopy 명령에서 사용하는 각 원본 또는 대상 URL에 SAS 토큰을 추가할 수 있습니다.

This example command recursively copies data from a local directory to a blob container. A fictitious SAS token is appended to the end of the container URL.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰에 대해 그리고 이를 얻는 방법에 대해 자세히 알아보려면 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 제한적인 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)를 참조하세요.

> <bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> The <bpt id="p2">[</bpt>Secure transfer required<ept id="p2">](storage-require-secure-transfer.md)</ept> setting of a storage account determines whether the connection to a storage account is secured with Transport Layer Security (TLS). This setting is enabled by default.   

## <a name="explore-the-help"></a>도움말 살펴보기

1. 명령 목록을 보려면 `azcopy -h`를 입력한 다음 Enter 키를 누릅니다.
2. 특정 명령에 대한 자세한 내용을 보려면 명령의 이름(예: `azcopy list -h`)을 포함시키고 ENTER 키를 누릅니다.

![인라인 도움말](Images/azcopy-inline-help.png)

## <a name="download-a-blob-from-blob-storage-to-the-file-system"></a>Blob Storage에서 파일 시스템으로 Blob 다운로드

>**참고:**
>- This example requires an Azure storage account with a blob container and blob file. You will also need to capture parameters in a text editor like Notepad.
>- This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

1. Azure Portal에 액세스합니다.
2. 다운로드하고자 하는 Blob이 있는 스토리지 계정에 액세스합니다.
3. 관심 있는 Blob으로 드릴다운하고 파일 **속성**을 확인합니다.
4. 관련 파일은 zip 파일(Windows 및 Mac) 또는 tar 파일(Linux)로 압축되어 있습니다.
5. Linux에서 tar 파일을 다운로드하고 압축을 풀려면 Linux 배포에 대한 설명서를 참조하세요.
6. 값을 사용하여 명령을 구성합니다.

    ```
    azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'
    ```
    
7. 오류가 발생하면 주의 깊게 읽은 다음 수정하세요.
8. Blob이 로컬 디렉터리로 다운로드되었는지 확인합니다. 

## <a name="upload-files-to-azure-blob-storage"></a>Azure Blob Storage에 파일 업로드

>**참고:**
>- 이 예제는 이전 예제와 이어지며, 파일이 있는 로컬 디렉터리를 필요로 합니다.
>- This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

1. 명령의 *<local-file-path>* 원본은 파일이 있는 로컬 디렉토리가 됩니다. 
2. The <bpt id="p1">*</bpt>https://&lt;storage-account-name&gt;.<ph id="ph1">&lt;blob or dfs&gt;</ph>.core.windows.net/&lt;container-name&gt;/&lt;blob-name&gt;<ept id="p1">*</ept> destination for the command will the blob URL used in the previous example. Be sure to remove the filename, just include the storage account and container. 
3. 값을 사용하여 명령을 구성합니다.

    ```
    azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'
    ```

5. 오류가 발생하면 주의 깊게 읽은 다음 수정하세요.
6. 로컬 파일이 Azure 컨테이너에 복사되었는지 확인합니다. 
7. 하위 디렉터리와 패턴 일치를 재귀적으로 사용하는 스위치가 있는지 확인합니다.
