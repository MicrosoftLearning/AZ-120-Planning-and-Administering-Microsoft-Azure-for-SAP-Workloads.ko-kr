# <a name="demonstration-work-with-powershell-locally"></a>데모: 로컬에서 PowerShell 작업

In this demonstration, we will install Azure Az PowerShell module. The Az module is available from a global repository called the <bpt id="p1">*</bpt>PowerShell Gallery<ept id="p1">*</ept>. You can install the module onto your local machine through the <bpt id="p1">**</bpt>Install-Module<ept id="p1">**</ept> command. You need an elevated PowerShell shell prompt to install modules from the PowerShell Gallery. 

## <a name="install-the-az-module"></a>Az 모듈 설치

1. **시작** 메뉴를 열고 **Windows PowerShell**을 입력합니다.
2. **Windows PowerShell** 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자로 실행**을 선택합니다.
3. **사용자 계정 컨트롤** 대화 상자에서 **예**를 선택합니다.
4. Type the following command, and then press Enter. This command installs the module for all users by default. (It's controlled by the scope parameter.) AllowClobber overwrites the previous PowerShell module. 

    ```
    Install-Module -Name Az -AllowClobber
    ```

## <a name="install-nuget-if-needed"></a>NuGet 설치(필요한 경우)

1. 설치한 NuGet 버전에 따라 최신 버전을 다운로드하고 설치하라는 메시지가 표시될 수 있습니다.
2. 메시지가 표시되면 NuGet 공급자를 설치하고 가져옵니다.

## <a name="trust-the-repository"></a>신뢰할 수 있는 리포지토리

1. 이 데모에서는 Azure Az PowerShell 모듈을 설치합니다.

    ```
    You are installing the modules from an untrusted repository. If you trust this repository, change its
    InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
    'PSGallery'?
    ```

2. 메시지가 표시되면 모듈을 설치합니다. 

## <a name="connect-to-azure-and-view-your-subscription-information"></a>Azure에 연결하여 구독 정보를 확인합니다.

1. Azure에 로그인

    ```
    Login-AzAccount
    ```

2. 메시지가 표시되면 자격 증명을 제공합니다.
3. 구독 정보를 확인합니다.

    ```
    Get-AzSubscription
    ```

## <a name="create-resources"></a>리소스 만들기

1. Az 모듈은 *PowerShell 갤러리*라는 전역 리포지토리에 있습니다.

    ```
    New-AzResourceGroup -name <name> -location <location>
    ```

2. 리소스 그룹을 확인합니다. 
  
    ```
    Get-AzResourceGroup
    ```

3. **Install-Module** 명령을 통해 모듈을 로컬 컴퓨터에 설치할 수 있습니다. 

    ```
    Remove-AzResourceGroup -Name Test
    ```