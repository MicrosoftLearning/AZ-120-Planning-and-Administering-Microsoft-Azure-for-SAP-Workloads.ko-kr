# 데모: AzCopy 살펴보기

## AzCopy 다운로드

첫째, AzCopy V10 실행 파일을 컴퓨터의 디렉터리로 다운로드합니다. AzCopy V10은 실행 파일이므로 아무것도 설치할 필요가 없습니다.

- [Windows 64비트](https://aka.ms/downloadazcopy-v10-windows)(zip)
- [Windows 32비트](https://aka.ms/downloadazcopy-v10-windows-32bit)(zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

이 파일들은 zip 파일(Windows 및 Mac) 또는 tar 파일(Linux)로 압축됩니다. Linux에서 tar 파일을 다운로드하고 압축 해제하려면 Linux 배포 문서를 참조하세요.

## AzCopy 실행

편의상, AzCopy 실행 파일의 디렉터리 위치를 시스템 경로에 추가하여 사용하기 쉽도록 하는 것이 좋습니다. 이렇게 하면 시스템의 모든 디렉터리에서 `azcopy`를 입력할 수 있습니다.

AzCopy 디렉터리를 경로에 추가하지 않도록 선택하는 경우 디렉터리를 AzCopy 실행 파일의 위치로 변경하고 Windows PowerShell 명령 프롬프트에서 `azcopy` 또는 `.\azcopy`를 입력해야 합니다.

Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. AzCopy로 무언가를 하기 전에 스토리지 서비스에 어떻게 권한 부여 자격 증명을 제공할 것인지 결정해야 합니다. 

## AzCopy 권한 부여

Azure Active Directory(AD) 또는 SAS(공유 액세스 서명) 토큰을 사용하여 권한 부여 자격 증명을 제공할 수 있습니다.

이 표를 가이드로 참조하세요.

| 스토리지 유형 | 현재 지원되는 권한 부여 방법 |
|--|--|
|**Blob Storage** | Azure AD 및 SAS |
|**Blob Storage(계층적 네임스페이스)** | Azure AD 및 SAS |
|**파일 저장소** | SAS 전용 |

### 옵션 1: Azure Active Directory 사용

이 옵션은 Blob Storage에만 사용할 수 있습니다. Azure Active Directory를 사용하면 각 명령에 SAS 토큰을 추가하는 대신 자격 증명을 한 번만 제공하면 됩니다.  

> **참고:** 현재 릴리스에서는 스토리지 계정 간에 Blob를 복사할 계획이면 SAS 토큰을 각 원본 URL에 추가해야 합니다. 대상 URL에서만 SAS 토큰을 생략할 수 있습니다. 예제는 [스토리지 계정 간에 Blob qhrtk](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-data)를 참조하세요.

Azure AD를 사용하여 액세스 권한을 부여하려면 [AzCopy 및 Azure AD(Azure Active Directory)를 사용하여 Blob에 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-authorize-azure-active-directory)를 참조하세요.

### 옵션 2: SAS 토큰 사용

AzCopy 명령에서 사용하는 각 원본 또는 대상 URL에 SAS 토큰을 추가할 수 있습니다.

이 예제 명령은 데이터를 로컬 디렉터리에서 Blob 컨테이너로 재귀적으로 복사합니다. 가상의 SAS 토큰이 컨테이너 URL의 끝에 추가됩니다.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰에 대해 그리고 이를 얻는 방법에 대해 자세히 알아보려면 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 제한적인 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)를 참조하세요.

> **참고:** 스토리지 계정의 [보안 전송 필요](storage-require-secure-transfer.md) 설정은 스토리지 계정 연결이 TLS(Transport Layer Security)를 통해 보안 처리되는지 여부를 결정합니다. 이 설정은 기본적으로 사용하도록 설정됩니다.   

## 도움말 살펴보기

1. 명령 목록을 보려면 `azcopy -h`를 입력한 다음 Enter 키를 누릅니다.
2. 특정 명령에 대한 자세한 내용을 보려면 명령의 이름(예: `azcopy list -h`)을 포함한 후에 Enter 키를 누릅니다.

![인라인 도우말](Images/azcopy-inline-help.png)

## Blob Storage에서 파일 시스템으로 Blob 다운로드

>**참고:**
>- 이 예시에서는 Blob 컨테이너 및 Blob 파일이 있는 Azure Storage 계정이 필요합니다. 또한 메모장과 같은 텍스트 편집기에서 매개 변수를 캡처해야 합니다.
>- 이 예제에서 경로 인수는 작은따옴표('') 안에 있습니다. Windows 명령 셸(cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용하세요. Windows 명령 셸(cmd.exe)을 사용하는 경우에는 작은따옴표('') 대신 큰따옴표("") 안에 경로 인수를 넣으세요.

1. Azure Portal에 액세스합니다.
2. 다운로드하고자 하는 Blob이 있는 스토리지 계정에 액세스합니다.
3. 관심 있는 Blob으로 드릴다운하고 파일 **속성**을 확인합니다.
4. **URL** 정보를 복사합니다. 이것이 원본 경로 *https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>* 가 됩니다.
5. 로컬 대상 디렉터리를 찾습니다. 이것이 *<local-file-path>* 값이 됩니다. 파일 이름도 필요합니다.
6. 값을 사용하여 명령을 구성합니다.

    ```
    azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'
    ```
    
7. 오류가 발생하면 주의 깊게 읽고 수정하세요.
8. Blob이 로컬 디렉터리로 다운로드되었는지 확인합니다. 

## Azure Blob Storage에 파일 업로드

>**참고:**
>- 이 예제는 이전 예제와 이어지며, 파일이 있는 로컬 디렉터리를 필요로 합니다.
>- 이 예제에서 경로 인수는 작은따옴표('') 안에 있습니다. Windows 명령 셸(cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용하세요. Windows 명령 셸(cmd.exe)을 사용하는 경우에는 작은따옴표('') 대신 큰따옴표("") 안에 경로 인수를 넣으세요.

1. 명령의 *<local-file-path>* 원본은 파일이 있는 로컬 디렉토리가 됩니다. 
2. 명령의 *https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>* 대상은 이전 예제에서 사용된 Blob URL이 됩니다. 파일 이름을 제거하고 스토리지 계정과 컨테이너를 포함하세요. 
3. 값을 사용하여 명령을 구성합니다.

    ```
    azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'
    ```

5. 오류가 발생하면 주의 깊게 읽고 수정하세요.
6. 로컬 파일이 Azure 컨테이너에 복사되었는지 확인합니다. 
7. 하위 디렉터리와 패턴 일치를 재귀적으로 사용하는 스위치가 있는지 확인합니다.
