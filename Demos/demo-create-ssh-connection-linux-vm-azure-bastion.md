# <a name="demonstration-create-an-ssh-connection-to-a-linux-vm-using-azure-bastion"></a>데모: Azure Bastion을 사용하여 Linux VM에 대한 SSH 연결 만들기

In this demo you will use the Azure portal and your username and password to create an SSH connection to a Linux VM located in an Azure virtual network. When you use Azure Bastion, your VMs don't require a client, agent, or additional software.

## <a name="prerequisites"></a>필수 조건

To complete this demo, you will require an Azure virtual network containing a Linux VM. For more information about how to create an Azure VM, see <bpt id="p1">[</bpt>Demonstration: Create a virtual machine in the portal<ept id="p1">](https://github.com/MicrosoftLearning/AZ-120-Planning-and-Administering-Microsoft-Azure-for-SAP-Workloads/blob/master/Demos/demo-create-virtual-machine-portal.md)</ept>, or <bpt id="p2">[</bpt>Demonstration: Create a virtual machine with PowerShell<ept id="p2">](https://github.com/MicrosoftLearning/AZ-120-Planning-and-Administering-Microsoft-Azure-for-SAP-Workloads/blob/master/Demos/demo-create-virtual-machine-powershell.md)</ept>.

이 데모에서는 Azure Portal과 사용자 이름 및 암호를 사용하여 Azure Virtual Network에서 Linux VM에 대한 SSH 연결을 만듭니다. 

### <a name="required-roles"></a>필요한 역할

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

SSH를 통해 Linux VM에 연결하려면 VM에 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: SSH (22) ***또는***
* 인바운드 포트: 사용자 지정 값(Azure Bastion을 통해 VM에 연결할 때 이 사용자 지정 포트를 지정해야 함)

> Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다.

## <a name="connect-using-username-and-password"></a>연결: 사용자 이름 및 암호 사용

1. Open the <bpt id="p1">[</bpt>Azure portal<ept id="p1">](https://portal.azure.com)</ept>. Navigate to the virtual machine that you want to connect to, select <bpt id="p1">**</bpt>Connect<ept id="p1">**</ept>, and then select <bpt id="p2">**</bpt>Bastion<ept id="p2">**</ept> from the dropdown.

    ![연결이 선택된 Azure Portal의 가상 머신에 대한 개요를 보여주는 스크린샷](Images/azure-bastion-connect.png)

1. Select <bpt id="p1">**</bpt>Use Bastion<ept id="p1">**</ept>. If you didn't provision Bastion for the virtual network, see <bpt id="p1">[</bpt>Configure Bastion<ept id="p1">](https://docs.microsoft.com/azure/bastion/quickstart-host-portal)</ept>.
1. **Azure Bastion을 사용하여 연결** 페이지에서 **사용자 이름**과 **암호**를 입력합니다.

    ![암호 인증을 보여주는 스크린샷](Images/azure-bastion-password.png)

1. **연결**을 선택하여 VM에 연결합니다.