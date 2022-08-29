# <a name="demonstration-explore-azure-blob-storage"></a>데모: Azure Blob Storage 살펴보기

>**참고**: 이 데모에서는 스토리지 계정이 필요합니다.

## <a name="create-a-container"></a>컨테이너 만들기

1. Azure Portal에서 스토리지 계정으로 이동합니다.
2. 스토리지 계정의 왼쪽 메뉴에서 **Blob 서비스** 섹션이 나올 때까지 스크롤한 다음, **Blob**을 선택합니다.
3. **+ 컨테이너** 단추를 선택합니다.
4. Type a <bpt id="p1">**</bpt>Name<ept id="p1">**</ept> for your new container. The container name must be lowercase, must start with a letter or number, and can include only letters, numbers, and the dash (-) character. 
5. Set the level of public access to the container. The default level is Private (no anonymous access).
6. **확인**을 선택하여 컨테이너를 만듭니다.

## <a name="upload-a-block-blob"></a>블록 Blob 업로드

1. Azure Portal에서 이전 섹션에서 만든 컨테이너로 이동합니다.
2. Select the container to show a list of blobs it contains. Since this container is new, it won't yet contain any blobs.
3. **업로드** 단추를 선택하여 BLOB을 컨테이너에 업로드합니다.
4. **고급** 섹션을 펼칩니다.
5. **인증 유형**, **BLOB 유형**, **블록 크기** 및 **폴더에 업로드**하는 기능을 확인합니다.
6. 기본 **인증 유형**은 SAS입니다.
4. 로컬 파일 시스템을 탐색하여 블록 Blob으로 업로드할 파일을 찾고 **업로드**를 선택합니다.
5. Upload as many blobs as you like in this way. You'll observe that the new blobs are now listed within the container.

## <a name="download-a-block-blob"></a>블록 Blob 다운로드

블록 Blob을 다운로드하여 브라우저에 표시하거나 로컬 파일 시스템에 저장할 수 있습니다. 

1. 이전 섹션에서 업로드한 BLOB 목록으로 이동합니다.
2. 다운로드할 Blob을 마우스 오른쪽 단추로 클릭하고 **다운로드**를 선택합니다.

>**참고**: 시간이 있다면 Blob Storage 관리를 위한 Azure Storage Explorer의 사용을 살펴보십시오. 