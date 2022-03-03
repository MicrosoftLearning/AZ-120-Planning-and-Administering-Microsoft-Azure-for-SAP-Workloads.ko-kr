---
ms.openlocfilehash: beb1c622d0a76e56d03dbbef534988edcdc44b93
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857713"
---
# <a name="demonstration-review-common-storage-explorer-tasks"></a>데모: 일반적인 Storage Explorer 작업 검토

>**참고**:
>- 이전 버전의 Storage Explorer가 설치되어 있다면 업그레이드해야 합니다. 이 단계에서는 버전 1.6.2를 사용합니다.
>- 데모 목적으로 기본 스토리지 계정 연결만 수행합니다.

## <a name="download-and-install-storage-explorer"></a>Storage Explorer 다운로드 및 설치

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 다운로드 및 설치 
2. 설치 후 도구를 시작합니다.
3. 릴리스 정보 및 메뉴 옵션을 검토합니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. Storage Explorer에서 왼쪽 상단 두 번째 아이콘인 **계정 관리** 를 선택합니다. 그러면 계정 관리 패널로 이동합니다.
2. 이제 왼쪽 창에는 사용자가 로그인한 모든 Azure 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가** 를 선택합니다.
3. 국내 클라우드 또는 Azure Stack에 로그인하려는 경우 Azure 환경 드롭다운을 클릭하여 사용하려는 Azure 클라우드를 선택합니다. 
4. 환경을 선택했으면 **로그인...** 단추를 클릭합니다. 
5. Azure Stack 계정으로 성공적으로 로그인하면 계정과 계정에 연결된 Azure Stack 구독이 왼쪽 창에 추가됩니다. 
6. 작업하려는 Azure 구독을 선택한 후 **적용** 을 선택합니다.
7. 왼쪽 창은 선택한 Azure 구독과 연결된 스토리지 계정을 표시합니다.

>**참고**: 다음 섹션에는 Azure 스토리지 계정이 있어야 합니다. 

## <a name="attach-an-azure-storage-account"></a>Azure Storage 계정 연결

1. Azure Portal에서 스토리지 계정에 액세스합니다.
2. **Storage Explorer** 에 대한 선택을 탐색합니다.
3. **선택키** 를 선택하고 키 사용 관련 정보를 읽습니다. 
4. Storage Explorer에서 연결하려면 **스토리지 계정 이름** 및 **Key1** 정보가 필요합니다.
5. Storage Explorer에서 **계정을 추가** 합니다.
6. 계정 이름을 계정 이름 텍스트 상자에 붙여 넣고 계정 키(Azure Portal의 key1)를 계정 키 입력란에 붙여 넣은 후 **다음** 을 선택합니다.
7. 탐색 창에서 스토리지 계정을 사용할 수 있는지 확인합니다. 페이지를 새로 고쳐야 할 수도 있습니다. 
8. 스토리지 계정에서 마우스 오른쪽 단추를 클릭하고 **포털에서 열기**, **기본 키 복사** 및 **빠른 액세스에 추가** 등 선택 사항을 확인합니다.