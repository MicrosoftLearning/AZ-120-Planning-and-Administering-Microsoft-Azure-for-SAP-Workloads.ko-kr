# <a name="demonstration-experiment-with-the-cloud-shell"></a>데모: Cloud Shell 실험

## <a name="configure-the-cloud-shell"></a>Cloud Shell 구성

1. **Azure Portal**에 액세스합니다.
2. 상단 배너에서 **Cloud Shell** 아이콘을 클릭합니다.
3. On the Welcome to the Shell page, notice your selections for Bash or PowerShell. Select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept>.
4. The Azure Cloud Shell requires an Azure file share to persist files. As you have time, click Learn more to obtain information about the Cloud Shell storage and the associated pricing.
5. **구독**을 선택하고 **스토리지 만들기**를 클릭합니다. 

## <a name="experiment-with-azure-powershell"></a>Azure PowerShell 실험

1. 스토리지가 만들어지고 계정이 초기화될 때까지 기다립니다.
2. PowerShell 프롬프트에서 구독을 보려면 **Get-AzSubscription**을 입력합니다.
3. 리소스 그룹 정보를 보려면 **Get-AzResourceGroup**을 입력합니다.

## <a name="experiment-with-the-bash-shell"></a>Bash 셸 실험

1. 드롭다운을 사용하여 **Bash** 셸로 전환하고 선택을 확인합니다.
2. At the Bash shell prompt, type <bpt id="p1">**</bpt>az account list<ept id="p1">**</ept> to view your subscriptions. Also, try tab completion. 
3. 리소스 정보를 보려면 **az resource list**를 입력합니다.

## <a name="experiment-with-the-cloud-editor"></a>클라우드 편집기로 실험

1. To use the Cloud Editor, type <bpt id="p1">**</bpt>code .<ept id="p1">**</ept>. You can also select the curly braces icon. 
2. Select a file from the left navigation pane. For example, <bpt id="p1">**</bpt>.profile<ept id="p1">**</ept>.
3. 편집기 상단 배너, 설정 선택(텍스트 크기 및 글꼴) 및 파일 업로드/다운로드를 확인합니다.
4. 저장, 편집기 닫기 및 파일 열기를 하려면 맨 오른쪽에 있는 줄임표( **...** )를 확인합니다.
5. 시간이 지남에 따라 실험한 다음 클라우드 편집기를 **닫습니다**. 
6. Cloud Shell 창을 닫습니다.