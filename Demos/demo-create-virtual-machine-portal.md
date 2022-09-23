# <a name="demonstration-create-a-virtual-machine-in-the-portal"></a>데모: 포털에서 가상 머신 만들기

이 데모에서는 포털에서 Windows 가상 머신을 만들고 해당 가상 머신에 액세스해보겠습니다.

## <a name="create-the-virtual-machine"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. In the search box above the list of Azure Marketplace resources, search for <bpt id="p1">**</bpt>Windows Server 2016 Datacenter<ept id="p1">**</ept>. After locating the image, click <bpt id="p1">**</bpt>Create<ept id="p1">**</ept>.
3. In the <bpt id="p1">**</bpt>Basics<ept id="p1">**</ept> tab, under <bpt id="p2">**</bpt>Project details<ept id="p2">**</ept>, make sure the correct subscription is selected and then choose to <bpt id="p3">**</bpt>Create new<ept id="p3">**</ept> resource group. Type <bpt id="p1">*</bpt>myResourceGroup<ept id="p1">*</ept> for the name.

    ![VM에 대한 새 리소스 그룹 만들기](Images/AZ103_Demo_Creating_VMs1.png)

4. Under <bpt id="p1">**</bpt>Instance details<ept id="p1">**</ept>, type <bpt id="p2">*</bpt>myVM<ept id="p2">*</ept> for the <bpt id="p3">**</bpt>Virtual machine name<ept id="p3">**</ept> and choose <bpt id="p4">*</bpt>East US<ept id="p4">*</ept> for your <bpt id="p5">**</bpt>Location<ept id="p5">**</ept>. Leave the other defaults.

    ![인스턴스 세부 정보 섹션](Images/AZ103_Demo_Creating_VMs2.png)

5. Under <bpt id="p1">**</bpt>Administrator account<ept id="p1">**</ept>, provide a username, such as <bpt id="p2">*</bpt>azureuser<ept id="p2">*</ept> and a password. The password must be at least 12 characters long and meet the defined complexity requirements.

    ![사용자 이름 및 암호 입력](Images/AZ103_Demo_Creating_VMs3.png)

6. **인바운드 포트 규칙**에서 **선택한 포트 허용**을 선택한 다음, 드롭다운에서 **RDP(3389)** 및 **HTTP**를 선택합니다.

    ![RDP 및 HTTP에 대한 포트 열기](Images/AZ103_Demo_Creating_VMs4.png)

7. Move to the <bpt id="p1">**</bpt>Management<ept id="p1">**</ept> tab, and under <bpt id="p2">**</bpt>Monitoring<ept id="p2">**</ept> turn <bpt id="p3">**</bpt>Off<ept id="p3">**</ept> Boot Diagnostics. This will eliminate validation errors. 
8. Azure Marketplace 리소스 목록 위의 검색 상자에서 **Windows Server 2016 Datacenter**를 검색합니다. 

    ![검토 후 만들기](Images/AZ103_Demo_Creating_VMs5.png)

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

이미지를 찾은 후 **만들기**를 클릭합니다.

1. 가상 머신 속성 페이지에서 **연결**단추를 선택합니다.
2. **가상 머신에 연결** 페이지에서, 3389 포트를 통해 DNS 이름으로 연결하는 기본 옵션을 유지하고 **RDP 파일 다운로드**를 클릭합니다.
3. 다운로드한 RDP 파일을 열고 메시지가 표시되면 **연결**을 선택합니다.
4. In the <bpt id="p1">**</bpt>Windows Security<ept id="p1">**</ept> window, select <bpt id="p2">**</bpt>More choices<ept id="p2">**</ept> and then <bpt id="p3">**</bpt>Use a different account<ept id="p3">**</ept>. Type the username as localhost\username, enter password you created for the virtual machine, and then select <bpt id="p1">**</bpt>OK<ept id="p1">**</ept>.
5. **기본** 탭의 **프로젝트 세부 정보** 아래에서 올바른 구독이 선택되어 있는지 확인한 다음, 리소스 그룹 **새로 만들기**를 선택합니다.

## <a name="install-web-server"></a>웹 서버 설치

이름으로 *myResourceGroup*을 입력합니다.

```PowerShell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

여기까지 마쳤으면 VM에 대한 RDP 연결을 닫습니다.

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

Portal에서 VM을 선택하고 VM의 개요 창에서 공용 IP 주소 오른쪽의 **클릭하여 복사** 단추를 사용하여 해당 주소를 복사한 다음 브라우저 탭에 붙여 넣습니다. 기본 IIS 시작 페이지가 열립니다.

![IIS 기본 사이트](Images/AZ103_Demo_Creating_VMs6.png)

## <a name="clean-up-resources"></a>리소스 정리

><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> When no longer needed, you can delete the resource group, virtual machine, and all related resources. To do so, select the resource group for the virtual machine, select <bpt id="p1">**</bpt>Delete<ept id="p1">**</ept>, then confirm the name of the resource group to delete.