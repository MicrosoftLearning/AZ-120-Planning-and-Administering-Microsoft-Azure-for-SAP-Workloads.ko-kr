---
ms.openlocfilehash: 8253f093bc365b521180798165190e879b37a9af
ms.sourcegitcommit: 0113753baec606c586c0bdf4c9452052a096c084
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2022
ms.locfileid: "137857659"
---
# <a name="demonstration-create-a-virtual-machine-with-powershell"></a>데모: PowerShell을 사용하여 가상 머신 만들기

이 데모에서는 PowerShell을 사용하여 가상 머신을 만듭니다.

## <a name="create-the-virtual-machine"></a>가상 머신 만들기

>**참고:** Cloud Shell 또는 로컬 버전의 PowerShell 을 사용할 수 있습니다. 

1. Cloud Shell 시작.
2. 코드를 실행합니다.

```
    # create a resource group
    New-AzResourceGroup -Name myResourceGroup -Location EastUS

    # create the virtual machine 
    # when prompted, provide a username and password to be used as the logon credentials for the VM
    New-AzVm `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress" `
        -OpenPorts 80,3389
```

## <a name="verify-the-machine-creation-in-the-portal"></a>포털에서 머신 만들기 확인

1. 포털에 액세스하고 가상 머신을 봅니다.
2. **myVM** 이 만들어졌는지 확인합니다.
3. VM 설정을 검토합니다. 
4. 이 머신은 새 VNet 및 서브넷의 Windows 머신입니다. 
5. 명령이 머신을 시작했습니다. 
6. 이 시점에서 포털 또는 PowerShell을 사용하여 변경할 수 있습니다. 

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

1. 머신의 공용 IP 주소를 검색합니다.

```
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```
2. 로컬 머신에서 RDP 세션을 만듭니다. IP 주소를 VM의 공용 IP 주소로 바꿉니다. cmd 창에서 다음 명령이 실행됩니다.

```
mstsc /v:publicIpAddress
```

3. 메시지가 표시되면 머신에 대한 로그인 자격 증명을 제공합니다. **다른 계정을 사용** 을 해야 합니다. 사용자 이름을 localhost\username으로 입력하고 가상 머신에서 만든 암호를 입력한 다음 **확인** 을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속** 을 선택하여 연결을 만듭니다.
4. 여기까지 마쳤으면 VM에 대한 RDP 연결을 닫습니다.
5. 리소스를 정리합니다. 이 작업은 몇 분 정도 소요되며 리소스 그룹과 가상 머신이 제거됩니다.

```
Remove-AzResourceGroup -Name myResourceGroup 
```