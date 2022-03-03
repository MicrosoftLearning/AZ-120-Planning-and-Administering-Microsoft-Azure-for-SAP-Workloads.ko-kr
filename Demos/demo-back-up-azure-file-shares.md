---
ms.openlocfilehash: 63b98ae1978da2870770c6a68e9c660aebecbf15
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857642"
---
# <a name="demonstration-back-up-azure-file-shares"></a>데모: Azure 파일 공유 백업

이 데모에서는 Azure Portal에서의 파일 공유 백업에 대해 살펴보겠습니다.

>**참고:** 이 데모를 진행하기 위해서는 자격 증명 모음에서 사용할 수 있는 스토리지 계정과 Azure 파일 공유가 필요합니다. 

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. Azure Portal에서 Recovery Services를 입력하고 **Recovery Services 자격 증명 모음** 을 클릭합니다.
3. **추가** 를 클릭합니다.
4. **이름**, **구독**, **리소스 그룹** 및 **위치** 를 입력합니다. 
5. 새 자격 증명 모음은 파일 공유와 동일한 위치에 있어야 합니다. 
5. **만들기** 를 클릭합니다. Recovery Services 자격 증명 모음을 만드는 데 몇 분 정도 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다. 
6. 몇 분 후 자격 증명 모음이 추가되지 않으면 **새로 고침** 을 클릭합니다.

## <a name="configure-the-vault"></a>자격 증명 모음 구성

1. Recovery Services 자격 증명 모음을 엽니다. 
2. **백업** 을 클릭하고 새 백업 인스턴스를 만듭니다. 
3. **워크로드가 실행되는 위치** 드롭다운 메뉴에서 **Azure** 를 선택합니다.
4. **백업할 항목** 메뉴에서 **Azure FileShare** 를 선택합니다.
5. **백업** 을 클릭합니다.
6. 스토리지 계정 목록에서 **스토리지 계정을 선택** 하고 **확인** 을 클릭합니다. Azure는 스토리지 계정에서 백업 가능한 파일 공유를 검색합니다. 최근에 파일 공유를 추가한 경우 파일 공유가 표시될 때까지 좀 기다려주세요.
7. File Shares 목록에서 백업할 **파일 공유 중 하나 이상을 선택하고** **확인** 을 클릭합니다.
8. 백업 정책 페이지에서 **새 백업 정책 만들기** 를 선택하고 이름, 스케줄 및 재방문 주기 정보를 제공합니다. **확인** 을 클릭합니다.
9. 백업 구성이 완료되면 **백업 사용** 을 클릭합니다. 

## <a name="explore-recovery-services-vault-information"></a>Recovery Services 자격 증명 모음 정보 살펴보기

1. **백업 항목** 블레이드를 탐색합니다. 백업된 항목과 복제된 항목에 대한 정보가 있습니다.
2. **백업 정책** 블레이드를 살펴보세요. 백업 정책을 추가하거나 삭제할 수 있습니다. 
3. **백업 작업** 블레이드를 탐색합니다. 여기에서 백업 작업의 상태를 검토할 수 있습니다.