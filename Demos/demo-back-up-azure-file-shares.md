# <a name="demonstration-back-up-azure-file-shares"></a>데모: Azure 파일 공유 백업

이 데모에서는 Azure Portal에서의 파일 공유 백업에 대해 살펴보겠습니다.

>**참고:** 이 데모를 진행하기 위해서는 자격 증명 모음에서 사용할 수 있는 스토리지 계정과 Azure 파일 공유가 필요합니다. 

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. Azure Portal에서 Recovery Services를 입력하고 **Recovery Services 자격 증명 모음**을 클릭합니다.
3. **추가**를 클릭합니다.
4. **이름**, **구독**, **리소스 그룹** 및 **위치**를 입력합니다. 
5. 새 자격 증명 모음은 파일 공유와 동일한 위치에 있어야 합니다. 
5. Click <bpt id="p1">**</bpt>Create<ept id="p1">**</ept>. It can take several minutes for the Recovery Services vault to be created. Monitor the status notifications in the upper right-hand area of the portal. Once your vault is created, it appears in the list of Recovery Services vaults. 
6. 몇 분 후 자격 증명 모음이 추가되지 않으면 **새로 고침**을 클릭합니다.

## <a name="configure-the-vault"></a>자격 증명 모음 구성

1. Recovery Services 자격 증명 모음을 엽니다. 
2. **백업**을 클릭하고 새 백업 인스턴스를 만듭니다. 
3. **워크로드가 실행되는 위치** 드롭다운 메뉴에서 **Azure**를 선택합니다.
4. **백업할 항목** 메뉴에서 **Azure FileShare**를 선택합니다.
5. **백업**을 클릭합니다.
6. From the list of Storage accounts, <bpt id="p1">**</bpt>select a storage account<ept id="p1">**</ept>, and click <bpt id="p2">**</bpt>OK<ept id="p2">**</ept>. Azure searches the storage account for files shares that can be backed up. If you recently added your file shares, allow a little time for the file shares to appear.
7. File Shares 목록에서 백업할 **파일 공유 중 하나 이상을 선택하고** **확인**을 클릭합니다.
8. On the Backup Policy page, choose <bpt id="p1">**</bpt>Create New backup policy<ept id="p1">**</ept> and provide Name, Schedule, and Retention information. Click <bpt id="p1">**</bpt>OK<ept id="p1">**</ept>.
9. 백업 구성이 완료되면 **백업 사용**을 클릭합니다. 

## <a name="explore-recovery-services-vault-information"></a>Recovery Services 자격 증명 모음 정보 살펴보기

1. Explore the <bpt id="p1">**</bpt>Backup items<ept id="p1">**</ept> blade. There is information on backed up items and replicated items.
2. Explore the <bpt id="p1">**</bpt>Backup policies<ept id="p1">**</ept> blade. You can add or delete backup policies. 
3. Explore the <bpt id="p1">**</bpt>Backup jobs<ept id="p1">**</ept> blade. Here you can review the status of your backup jobs.