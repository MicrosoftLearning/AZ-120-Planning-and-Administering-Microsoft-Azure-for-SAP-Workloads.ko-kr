---
ms.openlocfilehash: 64a17311a61ca8622ac5e6203ec5b63a0c169f57
ms.sourcegitcommit: 30dae3c49fe96a790479d08844a71fcb7851aa46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2022
ms.locfileid: "139867890"
---

# <a name="az-120-lab-prerequisites"></a>AZ 120: 랩 필수 구성 요소

## <a name="vcpu-core-requirements"></a>vCPU 코어 요구 사항

-   이 과정의 마지막 랩을 완료하려면 이 랩에 배포된 Azure VM이 상주할 가용성 영역을 지원하는 Azure 지역에서 vCPU 28개 이상이 제공되는 Microsoft Azure 구독이 필요합니다.

    -   4 x Standard_DS1_v2(각각 vCPU 1개) = 4

    -   6 x Standard_D4s_v3(각각 vCPU 4개) = 24

    > **참고**: 리소스 배포를 위해 **미국 동부** 또는 **미국 동부2** 지역을 사용하는 것이 좋습니다.

    > **참고**: 가용성 영역을 지원하는 Azure 지역을 식별하려면 [https://docs.microsoft.com/en-us/azure/availability-zones/az-overview ]<https://docs.microsoft.com/en-us/azure/availability-zones/az-overview> 를 참조하세요.

이 과정의 처음 3개 랩에서는 vCPU 요구 사항을 쉽게 충족할 수 있지만, 모든 랩의 요구 사항을 충족하도록 할당량 증가를 요청하는 것이 좋습니다. 할당량 증가 요청 자체는 대개 당일(영업일 기준)에 완료되지만, 할당량 증가 프로세스는 시간이 다소 걸리기 때문입니다.

## <a name="before-the-hands-on-lab"></a>실습 랩 시작 전 준비 사항

소요 시간: 30분

### <a name="task-1-validate-sufficient-number-of-vcpu-cores"></a>작업 1: 충분한 수의 vCPU 코어가 있는지 확인

1.  Azure Portal(<http://portal.azure.com>)에서 

1.  Azure Portal의 Cloud Shell에서 PowerShell 세션을 시작합니다. 

    > **참고**: 현재 Azure 구독에서 Cloud Shell을 처음 시작하는 경우 Cloud Shell 파일을 보관할 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 적용하면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다.

1.  Azure Portal의 **Cloud Shell** 창 내 PowerShell 프롬프트에서 다음 명령을 실행합니다. 여기서 `<Azure_region>`은 이 랩에 사용할 대상 Azure 지역을 가리킵니다(예: `eastus`).

    ```powershell
    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv3Family'}

    Get-AzVMUsage -Location '<Azure_region>' | Where-Object {$_.Name.Value -eq 'StandardDSv2Family'}
    ``` 

    > **참고**: Azure 지역의 이름을 확인하려면 **Cloud Shell** 의 Bash 프롬프트에서 `(Get-AzLocation).Location`을 실행합니다.
   
1.  이전 단계에서 실행한 명령 출력의 현재 값과 제한 항목을 검토하고 대상 Azure 지역에 충분한 수의 vCPU가 있는지 확인합니다.

1.  vCPU 수가 충분하지 않은 경우 Azure Portal에서 구독 블레이드로 다시 이동한 다음 **사용량 + 할당량** 을 클릭합니다. 

1.  구독의 **사용량 + 할당량** 블레이드에서 **증가 요청** 을 클릭합니다.

1.  **문제 설명** 블레이드에서 다음을 지정합니다.

    -   문제 유형: **서비스 및 구독 제한(할당량)**

    -   구독: 이 랩에서 사용할 Azure 구독의 이름

    -   할당량 유형: **Compute/VM(코어/vCPU) 구독 제한 늘리기**

1. **할당량 관리** 를 클릭합니다.

1. 위치 드롭다운 메뉴를 사용하여 사용하려는 Azure 지역으로 결과를 필터링합니다. **미국 동부** 또는 **미국 동부2** 를 사용하는 것이 좋습니다.

1. **표준 DSv3 제품군 vCPU** 할당량 유형을 찾고 편집 연필을 선택합니다.

1. 새 한도 필드에서 **40** 을 지정하고 **저장 및 계속** 을 클릭합니다.

1. **총 지역 vCPU** 할당량 유형을 찾고 편집 연필을 선택합니다.

1. 새 한도 필드에서 **40** 을 지정하고 **저장 및 계속** 을 클릭합니다.

   > **참고**: 할당량 증가 요청은 자동으로 승인되어야 합니다. 요청이 거부되면 지원 티켓을 열어 할당량 증가를 요청합니다.
