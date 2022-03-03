---
ms.openlocfilehash: 5673f081ce1335404538fd8bb648084293cb873c
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857643"
---
# <a name="demonstration-explore-azure-blob-storage"></a>데모: Azure Blob Storage 살펴보기

>**참고**: 이 데모에서는 스토리지 계정이 필요합니다.

## <a name="create-a-container"></a>컨테이너 만들기

1. Azure Portal에서 스토리지 계정으로 이동합니다.
2. 스토리지 계정의 왼쪽 메뉴에서 **Blob 서비스** 섹션이 나올 때까지 스크롤한 다음, **Blob** 을 선택합니다.
3. **+ 컨테이너** 단추를 선택합니다.
4. 새 컨테이너의 **이름** 을 입력합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다. 
5. 컨테이너에 대한 공용 액세스 수준을 설정합니다. 기본 수준은 프라이빗(익명 액세스 없음) 입니다.
6. **확인** 을 선택하여 컨테이너를 만듭니다.

## <a name="upload-a-block-blob"></a>블록 Blob 업로드

1. Azure Portal에서 이전 섹션에서 만든 컨테이너로 이동합니다.
2. 컨테이너를 선택하여 그 안에 있는 BLOB 목록을 표시합니다. 이 컨테이너는 새 컨테이너이므로 아직 포함된 Blob이 없습니다.
3. **업로드** 단추를 선택하여 BLOB을 컨테이너에 업로드합니다.
4. **고급** 섹션을 펼칩니다.
5. **인증 유형**, **BLOB 유형**, **블록 크기** 및 **폴더에 업로드** 하는 기능을 확인합니다.
6. 기본 **인증 유형** 은 SAS입니다.
4. 로컬 파일 시스템을 탐색하여 블록 Blob으로 업로드할 파일을 찾고 **업로드** 를 선택합니다.
5. 이렇게 원하는 만큼의 BLOB을 업로드합니다. 이제 새 Blob이 컨테이너에 표시됩니다.

## <a name="download-a-block-blob"></a>블록 Blob 다운로드

블록 Blob을 다운로드하여 브라우저에 표시하거나 로컬 파일 시스템에 저장할 수 있습니다. 

1. 이전 섹션에서 업로드한 BLOB 목록으로 이동합니다.
2. 다운로드할 Blob을 마우스 오른쪽 단추로 클릭하고 **다운로드** 를 선택합니다.

>**참고**: 시간이 있다면 Blob Storage 관리를 위한 Azure Storage Explorer의 사용을 살펴보십시오. 