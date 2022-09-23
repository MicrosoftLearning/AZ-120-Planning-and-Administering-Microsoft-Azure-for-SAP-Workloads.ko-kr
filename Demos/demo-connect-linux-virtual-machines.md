# <a name="demonstration-connect-to-linux-virtual-machines"></a>데모: Linux 가상 머신에 연결

이 데모에서는 Linux 컴퓨터를 만들고 SSL을 통해 해당 컴퓨터에 액세스합니다.

>**참고:** 연결이 작동하도록 하려면 포트 22가 열려 있는지 확인합니다. 

## <a name="create-the-ssh-keys"></a>SSH 키 만들기

1. Download the PuTTY tool. This will include <bpt id="p1">[</bpt>PuTTYgen<ept id="p1">](https://putty.org/)</ept>. 
2. 설치되면 **PuTTYgen** 프로그램을 찾아서 엽니다.
3. **매개 변수** 옵션 그룹에서 **RSA**를 선택합니다.
4. **생성** 단추를 클릭합니다.
5. 창의 빈 영역 주위로 마우스를 이동하여 임의성을 생성합니다.
6. **권한 있는 키 파일에 붙여넣기 위한 공개 키**의 텍스트를 복사합니다.
7. Optionally you can specify a <bpt id="p1">**</bpt>Key passphrase<ept id="p1">**</ept> and then <bpt id="p2">**</bpt>Confirm passphrase.<ept id="p2">**</ept> You will be prompted for the passphrase when you authenticate to the VM with your private SSH key. Without a passphrase, if someone obtains your private key, they can sign in to any VM or service that uses that key. We recommend you create a passphrase. However, if you forget the passphrase, there is no way to recover it.
8. **프라이빗 키 저장**을 클릭합니다.
9. Choose a location and filename and click <bpt id="p1">**</bpt>Save<ept id="p1">**</ept>. You will need this file to access the VM. 

## <a name="create-the-linux-machine-and-assign-the-public-ssh-key"></a>Linux 머신 만들기 및 공개 SSH 키 할당

1. 포털에서 원하는 Linux 컴퓨터를 만듭니다.
2. **인증 유형**에 (**암호** 대신) **SSH 공개 키**를 선택합니다.
3. **사용자 이름**을 입력합니다.
4. Paste the public SSH key from PuTTY into the <bpt id="p1">**</bpt>SSH public key<ept id="p1">**</ept> text area. Ensure the key validates with a checkmark. 
5. Create the VM. Wait for it to deploy.
6. 실행 중인 VM에 액세스합니다. 
7. **개요** 창에서 **연결**을 클릭합니다.
8. 사용자 및 공용 IP 주소를 포함하여 로그인 정보를 기록해 둡니다.

## <a name="access-the-server-using-ssh"></a>SSH를 사용하여 서버에 액세스

1. **PuTTY** 도구를 엽니다.
2. **username@publicIpAddress**를 입력합니다. 여기서 username은 VM을 만들 때 할당한 값이고 publicIpAddress는 Azure Portal에서 가져온 값입니다.
3. **포트**에 **22**를 지정합니다.
4. **연결 형식** 옵션 그룹에서 **SSH**를 선택합니다.
5. 범주 패널에서 **SSH**로 이동한 다음 **인증**을 클릭합니다.
6. **인증을 위한 프라이빗 키 파일** 옆에 있는 **찾아보기** 단추를 클릭합니다.
7. SSH 키를 생성할 때 저장된 프라이빗 키 파일로 이동하여 **열기**를 클릭합니다.
8. 주 PuTTY 화면에서 **열기**를 클릭합니다.
9. 이제 서버 명령줄에 연결됩니다. 