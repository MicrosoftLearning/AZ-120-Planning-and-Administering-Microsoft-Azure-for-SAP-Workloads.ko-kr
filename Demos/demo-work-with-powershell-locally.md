---
ms.openlocfilehash: f7dfd65b73dd2e48b37b7d4e26ed86a4a82463ae
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857646"
---
# <a name="demonstration-work-with-powershell-locally"></a>데모: 로컬에서 PowerShell 작업

이 데모에서는 Azure Az PowerShell 모듈을 설치합니다. Az 모듈은 *PowerShell 갤러리* 라는 전역 리포지토리에 있습니다. **Install-Module** 명령을 통해 모듈을 로컬 컴퓨터에 설치할 수 있습니다. PowerShell 갤러리에서 모듈을 설치하려면 관리자 권한으로 실행하는 PowerShell 셸 프롬프트가 필요합니다. 

## <a name="install-the-az-module"></a>Az 모듈 설치

1. **시작** 메뉴를 열고 **Windows PowerShell** 을 입력합니다.
2. **Windows PowerShell** 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자로 실행** 을 선택합니다.
3. **사용자 계정 컨트롤** 대화 상자에서 **예** 를 선택합니다.
4. 다음 명령을 입력한 후 Enter 키를 누릅니다. 이 명령은 기본적으로 모든 사용자를 위한 모듈을 설치합니다. (범위 매개 변수로 이 명령을 제어할 수 있습니다.) AllowClobber는 이전 PowerShell 모듈을 덮어씁니다. 

    ```
    Install-Module -Name Az -AllowClobber
    ```

## <a name="install-nuget-if-needed"></a>NuGet 설치(필요한 경우)

1. 설치한 NuGet 버전에 따라 최신 버전을 다운로드하고 설치하라는 메시지가 표시될 수 있습니다.
2. 메시지가 표시되면 NuGet 공급자를 설치하고 가져옵니다.

## <a name="trust-the-repository"></a>신뢰할 수 있는 리포지토리

1. 기본적으로 PowerShell 갤러리는 PowerShellGet의 신뢰할 수 있는 리포지토리로 구성되지 않습니다. PowerShell 갤러리를 처음 사용하면 다음과 같은 메시지가 표시됩니다.

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

1. 새 리소스 그룹을 만듭니다. 원하는 경우 다른 위치를 제공합니다. *이름* 은 구독 내에서 고유해야 합니다. *위치* 는 리소스 그룹의 메타데이터가 저장될 위치를 결정합니다. “미국 서부”, “북유럽” 또는 “인도 서부” 같은 문자열을 사용하여 위치를 지정합니다. westus, northeurope 또는 westindia 같이 동일한 의미의 단일 단어를 사용할 수도 있습니다. 핵심 구문은 다음과 같습니다.

    ```
    New-AzResourceGroup -name <name> -location <location>
    ```

2. 리소스 그룹을 확인합니다. 
  
    ```
    Get-AzResourceGroup
    ```

3. 리소스 그룹을 제거합니다. 메시지가 표시되면 확인합니다. 

    ```
    Remove-AzResourceGroup -Name Test
    ```