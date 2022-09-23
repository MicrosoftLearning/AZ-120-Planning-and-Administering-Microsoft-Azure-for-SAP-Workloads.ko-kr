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
2. **myVM**이 만들어졌는지 확인합니다.
3. VM 설정을 검토합니다. 
4. 이 머신은 새 VNet 및 서브넷의 Windows 머신입니다. 
5. 명령이 머신을 시작했습니다. 
6. 이 시점에서 포털 또는 PowerShell을 사용하여 변경할 수 있습니다. 

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

1. 머신의 공용 IP 주소를 검색합니다.

```
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```
2. Create an RDP session from your local machine. Replace the IP address with the public IP address of your VM. This command runs from a cmd window.

```
mstsc /v:publicIpAddress
```

3. When prompted, provide your login credentials for the machine. Be sure to <bpt id="p1">**</bpt>Use a different account<ept id="p1">**</ept>. Type the username as localhost\username, enter password you created for the virtual machine, and then select <bpt id="p1">**</bpt>OK<ept id="p1">**</ept>. You may receive a certificate warning during the sign-in process. Select <bpt id="p1">**</bpt>Yes<ept id="p1">**</ept> or <bpt id="p2">**</bpt>Continue<ept id="p2">**</ept> to create the connection
4. 여기까지 마쳤으면 VM에 대한 RDP 연결을 닫습니다.
5. Clean up your resources. This will take a few minutes and remove the resource group and virtual machine.

```
Remove-AzResourceGroup -Name myResourceGroup 
```