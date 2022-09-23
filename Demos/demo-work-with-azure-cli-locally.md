# <a name="demonstration-work-with-azure-cli-locally"></a>데모: 로컬에서 Azure CLI 작업

이 데모에서는 CLI를 설치하고 사용하여 리소스를 만듭니다.

## <a name="install-the-cli-on-windows"></a>Windows에 CLI 설치

MSI 설치 관리자를 사용하여 Windows 운영 체제에 Azure CLI를 설치합니다.

1. [MSI 설치 관리자](https://aka.ms/installazurecliwindows)를 다운로드하고 브라우저 보안 대화 상자에서 **실행**을 클릭합니다.
2. 설치 프로그램에서 라이선스 이용 약관에 동의한 후에 **설치**를 클릭합니다.
3. **사용자 계정 컨트롤** 대화 상자에서 **예**를 선택합니다.

## <a name="verify-azure-cli-installation"></a>Azure CLI 설치 확인

Linux 또는 macOS에서는 Bash 쉘을 열고, Windows에서는 명령 프롬프트 또는 PowerShell을 열어 Azure CLI를 실행합니다.

Azure CLI를 시작하고 버전 검사를 실행하여 설치를 확인합니다.

```azurecli
az --version
 ```

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Running Azure CLI from PowerShell has some advantages over running Azure CLI from the Windows command prompt. PowerShell provides more tab completion features than the command prompt.

## <a name="login-to-azure"></a>Azure에 로그인

Because you're working with a local Azure CLI installation, you'll need to authenticate before you can execute Azure commands. You do this by using the Azure CLI <bpt id="p1">**</bpt>login<ept id="p1">**</ept> command:

```azurecli
az login
```

Azure CLI will typically launch your default browser to open the Azure sign-in page. If this doesn't work, follow the command-line instructions and enter an authorization code at <bpt id="p1">[</bpt><ph id="ph1">https://aka.ms/devicelogin</ph><ept id="p1">](https://aka.ms/devicelogin)</ept>.

로그인이 성공하면 Azure 구독에 연결됩니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

새 Azure 서비스를 만들려면 먼저 새 리소스 그룹을 만들어야 하므로 CLI에서 Azure 리소스를 만드는 방법을 보여 주는 예로 리소스 그룹을 사용하겠습니다.

Azure CLI <bpt id="p1">**</bpt>group create<ept id="p1">**</ept> command creates a resource group. You must specify a name and location. The <bpt id="p1">*</bpt>name<ept id="p1">*</ept> must be unique within your subscription. The <bpt id="p1">*</bpt>location<ept id="p1">*</ept> determines where the metadata for your resource group will be stored. You use strings like "West US", "North Europe", or "West India" to specify the location; alternatively, you can use single word equivalents, such as westus, northeurope, or westindia. The core syntax is:

```azurecli
az group create --name <name> --location <location>
```

## <a name="verify-the-resource-group"></a>리소스 그룹 확인하기

For many Azure resources,  Azure CLI provides a <bpt id="p1">**</bpt>list<ept id="p1">**</ept> subcommand to view resource details. For example, the Azure CLI <bpt id="p1">**</bpt>group list<ept id="p1">**</ept> command lists your Azure resource groups. This is useful to verify whether resource group creation was successful:

```azurecli
az group list
```

보기를 더 간결하게 하려면 출력의 형식을 단순한 테이블로 지정할 수 있습니다.

```azurecli
az group list --output table
```

If you have several items in the group list, you can filter the return values by adding a <bpt id="p1">**</bpt>query<ept id="p1">**</ept> option. Try this command:

```azurecli
az group list --query "[?name == '<rg name>']"
```

><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> You format the query using <bpt id="p2">**</bpt>JMESPath<ept id="p2">**</ept>, which is a standard query language for JSON requests. Learn more about this powerful filter language at <bpt id="p1">[</bpt><ph id="ph1">http://jmespath.org/</ph><ept id="p1">](http://jmespath.org/)</ept>.