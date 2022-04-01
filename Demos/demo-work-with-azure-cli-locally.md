---
ms.openlocfilehash: 5acbb0fe25c7ef36bcf0f85ba38547d4d8c419ba
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857664"
---
# <a name="demonstration-work-with-azure-cli-locally"></a>데모: 로컬에서 Azure CLI 작업

이 데모에서는 CLI를 설치하고 사용하여 리소스를 만듭니다.

## <a name="install-the-cli-on-windows"></a>Windows에 CLI 설치

MSI 설치 관리자를 사용하여 Windows 운영 체제에 Azure CLI를 설치합니다.

1. [MSI 설치 관리자](https://aka.ms/installazurecliwindows)를 다운로드하고 브라우저 보안 대화 상자에서 **실행** 을 클릭합니다.
2. 설치 프로그램에서 라이선스 이용 약관에 동의한 후에 **설치** 를 클릭합니다.
3. **사용자 계정 컨트롤** 대화 상자에서 **예** 를 선택합니다.

## <a name="verify-azure-cli-installation"></a>Azure CLI 설치 확인

Linux 또는 macOS에서는 Bash 쉘을 열고, Windows에서는 명령 프롬프트 또는 PowerShell을 열어 Azure CLI를 실행합니다.

Azure CLI를 시작하고 버전 검사를 실행하여 설치를 확인합니다.

```azurecli
az --version
 ```

>**참고**: PowerShell에서 Azure CLI를 실행하면 Windows 명령 프롬프트에서 Azure CLI를 실행하는 것보다 몇 가지 이점이 있습니다. PowerShell은 명령 프롬프트보다 더 많은 탭 완료 기능을 제공합니다.

## <a name="login-to-azure"></a>Azure에 로그인

로컬 Azure CLI 설치를 사용 중이므로 Azure 명령을 실행하려면 인증해야 합니다. Azure CLI **login** 명령을 사용하여 이 작업을 수행합니다.

```azurecli
az login
```

Azure CLI는 일반적으로 기본 브라우저를 실행하여 Azure 로그인 페이지를 엽니다. 그러지 않는 경우 명령줄 지침에 따라 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에 인증 코드를 입력합니다.

로그인이 성공하면 Azure 구독에 연결됩니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

새 Azure 서비스를 만들려면 먼저 새 리소스 그룹을 만들어야 하므로 CLI에서 Azure 리소스를 만드는 방법을 보여 주는 예로 리소스 그룹을 사용하겠습니다.

Azure CLI 명령어 **group create** 를 사용하여 리소스 그룹을 만듭니다. 이름 및 위치를 지정해야 합니다. *이름* 은 구독 내에서 고유해야 합니다. *위치* 는 리소스 그룹의 메타데이터가 저장될 위치를 결정합니다. “미국 서부”, “북유럽” 또는 “인도 서부” 같은 문자열을 사용하여 위치를 지정합니다. westus, northeurope 또는 westindia 같이 동일한 의미의 단일 단어를 사용할 수도 있습니다. 핵심 구문은 다음과 같습니다.

```azurecli
az group create --name <name> --location <location>
```

## <a name="verify-the-resource-group"></a>리소스 그룹 확인하기

Azure CLI는 많은 Azure 리소스에 대해 리소스 세부 정보를 볼 수 있는 하위 명령어인 **목록** 을 제공합니다. 예를 들어 Azure CLI **group list** 명령은 Azure 리소스 그룹을 나열합니다. 이 명령은 리소스 그룹이 성공적으로 만들어졌는지 확인하는 데 유용합니다.

```azurecli
az group list
```

보기를 더 간결하게 하려면 출력의 형식을 단순한 테이블로 지정할 수 있습니다.

```azurecli
az group list --output table
```

그룹 목록에 여러 항목이 있는 경우 **쿼리** 옵션을 추가하여 반환 값을 필터링할 수 있습니다. 다음 명령을 사용하세요.

```azurecli
az group list --query "[?name == '<rg name>']"
```

>**참고:** JSON 요청의 표준 쿼리 언어인 **JMESPath** 를 사용하여 쿼리 형식을 지정합니다. 이 강력한 필터 언어에 대한 자세한 내용은 [http://jmespath.org/](http://jmespath.org/)를 참조하세요.