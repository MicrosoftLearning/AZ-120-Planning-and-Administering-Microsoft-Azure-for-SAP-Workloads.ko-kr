---
ms.openlocfilehash: 9feaf88d7197664c2e81bab4247f601471ba416b
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857662"
---
# <a name="demonstration-create-an-azure-policy"></a>데모: Azure Policy 만들기

## <a name="assign-a-policy"></a>정책 할당

1. **모든 서비스** 를 클릭한 후 **정책** 을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다. 이 서비스는 **관리 및 거버넌스** 하에 있습니다.
2. Azure Policy 페이지의 왼쪽에서 **할당** 을 선택합니다. 할당은 특정 범위 내에서 수행하도록 할당된 정책입니다.
3. 정책 - 할당 창의 위쪽에서 **정책 할당** 을 선택합니다.
4. 정책 할당을 적용할 리소스 또는 리소스 그룹을 결정하는 **범위** 를 확인합니다.
5. **정책 정의...** 를 선택하여 사용 가능한 정의 목록을 엽니다. 시간을 할애하여 기본 제공 정책 정의를 검토하십시오.
6. **SQL Server 버전 12.0 필요** 를 선택합니다. 이 정책은 모든 SQL 서버가 버전 12.0을 사용하도록 합니다. 이 정책 정의를 사용할 수 없는 경우 다른 것을 선택합니다.
7. **관리 ID** 만들기는 선택 해제 상태로 둡니다. 
8. **할당** 을 클릭하여 정책을 만듭니다.

## <a name="create-and-assign-an-initiative-definition"></a>이니셔티브 정의 만들기 및 할당

1. Azure Policy 페이지의 왼쪽에 있는 작성에서 **정의** 를 선택합니다.
2. 페이지 위쪽에서 **+ 이니셔티브 정의** 를 선택하여 이니셔티브 정의 페이지를 엽니다.
3. **이름** 및 **설명** 을 제공합니다.
4. **새 범주 만들기**.
5. 오른쪽 패널에서 **SQL Server 12.0 버전 필요** 정책을 **추가** 합니다.
6. 선택한 정책을 하나 더 추가합니다.
7. 변경 내용을 **저장** 한 다음 구독에 이니셔티브 정의 사항을 **할당** 합니다.

## <a name="check-for-compliance"></a>규정 준수 확인

1. Azure Policy 서비스 페이지로 돌아갑니다.
2. **규정 준수** 를 선택합니다.
3. 정책 상태 및 정의를 검토합니다. 