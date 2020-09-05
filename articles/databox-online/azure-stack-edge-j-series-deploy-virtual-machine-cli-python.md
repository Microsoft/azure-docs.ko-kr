---
title: Azure CLI 및 Python을 통해 Azure Stack Edge 디바이스 GPU에 VM 배포
description: Azure CLI 및 Python을 사용하여 Azure Stack Edge GPU 디바이스에서 VM(가상 머신)을 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 464c0fee31f86ba6ffa1dbecc7b2dd659cd86685
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255531"
---
# <a name="deploy-vms-on-your-azure-stack-edge-gpu-device-using-azure-cli-and-python"></a>Azure CLI 및 Python을 사용하여 Azure Stack Edge GPU 디바이스에 VM 배포

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

이 자습서에서는 Azure CLI(명령줄 인터페이스)와 Python을 사용하여 Azure Stack Edge 디바이스에서 VM을 만들고 관리하는 방법을 설명합니다.

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

다음 다이어그램에 배포 워크플로가 나와 있습니다.

![VM 배포 워크플로](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

배포 워크플로에 대한 개략적인 요약은 다음과 같습니다.

1. Azure Resource Manager에 연결
2. 리소스 그룹 만들기
3. 스토리지 계정 만들기
4. 호스트 파일에 Blob URI 추가
5. 인증서 설치
6. VHD 업로드
7. VHD에서 관리 디스크 만들기
8. 이미지 관리 디스크에서 VM 이미지 만들기
9. 이전에 만든 리소스로 VM 만들기
10. VNet 만들기
11. VNet 서브넷 ID를 사용하여 VNIC 만들기

워크플로 다이어그램에 대한 자세한 설명은 [Azure PowerShell을 사용하여 Azure Stack Edge 디바이스에 VM 배포](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)를 참조하세요. Azure Resource Manager에 연결하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure Resource Manager에 연결](azure-stack-edge-j-series-connect-resource-manager.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure CLI와 Python을 사용하여 Azure Stack Edge 디바이스에서 VM 만들기 및 관리를 시작하기 전에, 다음 단계에 나열된 사전 요구 사항을 완료했는지 확인해야 합니다.

1. [1단계: Azure Stack Edge 디바이스 구성](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)의 설명대로 Azure Stack Edge 디바이스에서 네트워크 설정을 완료했습니다.

2. 컴퓨팅을 위한 네트워크 인터페이스를 사용하도록 설정했습니다. 이 네트워크 인터페이스 IP는 VM 배포를 위한 가상 스위치를 만드는 데 사용됩니다. 이 프로세스를 안내하는 단계는 다음과 같습니다.

    1. **Compute**로 이동합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    2. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. 이 네트워크 인터페이스에 해당하는 가상 스위치를 Azure Stack Edge가 만들고 관리합니다.

    컴퓨팅에 다른 네트워크 인터페이스를 사용하려면, 다음을 수행해야 합니다.

    - Azure Resource Manager를 사용하여 배포한 모든 VM을 삭제합니다.

    - 가상 네트워크 인터페이스 및 이 네트워크 인터페이스와 연결된 가상 네트워크를 모두 삭제합니다.

    - 이제 컴퓨팅에 다른 네트워크 인터페이스를 사용하도록 설정할 수 있습니다.

3. Azure Stack Edge 디바이스와 클라이언트의 신뢰할 수 있는 저장소에 모든 인증서를 만들고 설치했습니다. [2단계: 인증서 만들기 및 설치](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)에 설명된 절차를 따릅니다.

4. Azure Stack Edge 디바이스에 대해 Base-64로 인코딩된 *.cer* 인증서(PEM 형식)를 만들었습니다. 디바이스에 서명 체인으로 이미 업로드되었으며 클라이언트의 신뢰할 수 있는 루트 저장소에 설치되어 있습니다. 이 인증서가 클라이언트에서 작동하려면 Python용 *pem* 형식도 필요합니다.

    `certutil` 명령을 사용하여 이 인증서를 pem 형식으로 변환합니다. 인증서가 포함된 디렉터리에서 이 명령을 실행해야 합니다.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    다음 샘플은 명령 사용법을 보여줍니다.

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    이 pem을 나중에 Python 저장소에도 추가합니다.

5. 디바이스의 로컬 웹 UI에 있는 **네트워크** 페이지에서 디바이스 IP를 할당했습니다. 이 IP를 다음에 추가해야 합니다.

    - 클라이언트의 호스트 파일 또는
    - DNS 서버 구성
    
    > [!IMPORTANT]
    > 엔드포인트 이름 확인을 위해 DNS 서버 구성을 수정하는 것이 좋습니다.

    1. **메모장**을 관리자(파일을 저장하려면 관리자 권한이 필요함) 권한으로 시작한 다음, `C:\Windows\System32\Drivers\etc`에 있는 **hosts** 파일을 엽니다.
    
        ![Windows 탐색기 호스트 파일](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. 다음 항목을 디바이스에 적합한 값으로 대체하고 **hosts** 파일에 추가합니다.
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. 다음 이미지를 참조하세요. **호스트** 파일을 저장합니다.

        ![메모장의 hosts 파일](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. 이 절차에 사용된 [Python 스크립트를 다운로드](https://aka.ms/ase-vm-python)합니다.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>1단계: 클라이언트에서 Azure CLI/Python 설정

### <a name="verify-profile-and-install-azure-cli"></a>프로필 확인 및 Azure CLI 설치

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. 클라이언트에 Azure CLI를 설치합니다. 이 예제에서는 Azure CLI 2.0.80이 설치되었습니다. Azure CLI의 버전을 확인하려면 `az --version` 명령을 실행합니다.

    다음은 위 명령의 샘플 출력입니다.

    ```powershell
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Azure CLI가 없으면 [Windows에서 Azure CLI를 다운로드하여 설치](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)합니다. Windows 명령 프롬프트 또는 Windows PowerShell을 통해 Azure CLI를 실행할 수 있습니다.

2. CLI의 Python 위치를 기록해 둡니다. 이 정보는 Azure CLI에 대한 신뢰할 수 있는 루트 인증서 저장소의 위치를 확인하는 데 필요합니다.

3. 이 문서에 사용된 샘플 스크립트를 실행하려면 다음 Python 라이브러리 버전이 필요합니다.

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    해당 버전을 설치하려면 다음 명령을 실행합니다.

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    다음 샘플 출력은 Haikunator 설치를 보여줍니다.

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    다음 샘플 출력은 `msrestazure`의 pip 설치를 보여줍니다. 
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-ca-root-certificate"></a>Azure Stack Edge CA 루트 인증서 신뢰

1. 컴퓨터에서 인증서 위치를 찾습니다. 위치는 `az cli`를 설치한 위치에 따라 다를 수 있습니다. 관리자 권한으로 Windows PowerShell을 실행합니다. `az cli`가 Python을 설치한 경로(`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`)로 전환합니다.

    인증서 위치를 얻으려면 다음 명령을 입력합니다.

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    cmdlet은 인증서 위치를 아래와 같이 반환합니다.  
        
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    나중에 사용할 수 있도록 이 위치를 기록해 둡니다. - `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Azure Stack Edge CA 루트 인증서를 기존 Python 인증서에 추가하여 신뢰합니다. 이전에 PEM 인증서를 저장한 경로를 제공합니다.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    경로의 예는 "C:\VM-scripts\rootteam3device.pem"입니다.
    
    그런 다음, Windows PowerShell에 다음과 같은 일련의 명령을 입력합니다.

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge"></a>Azure Stack Edge에 연결

1. `az cloud register` 명령을 실행하여 Azure Stack Edge 환경을 등록합니다.

    일부 시나리오에서는 직접 아웃바운드 인터넷 연결이 SSL 인터셉션을 적용하는 프록시 또는 방화벽을 통해 라우팅됩니다. 이런 경우 az cloud register 명령이 \"클라우드에서 엔드포인트를 가져올 수 없습니다.\"와 같은 오류로 인해 실패할 수 있습니다. 이러한 오류를 해결하려면 Windows PowerShell에서 다음 환경 변수를 설정합니다.

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Azure Resource Manager 엔드포인트, 리소스가 생성된 위치 및 원본 VHD가 있는 경로에 대한 스크립트의 환경 변수를 설정합니다. 리소스의 위치는 모든 Azure Stack Edge 디바이스에서 고정되며 `dbelocal`로 설정됩니다. 주소 접두사 및 개인 IP 주소도 지정해야 합니다. 다음 환경 변수는 모두 `"dbelocal"`로 하드코딩되어야 하는 `AZURE_RESOURCE_LOCATION`을 제외하고 사용자 값에 기반한 값입니다.

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. 환경을 등록합니다. az cloud register를 실행하는 경우 다음 매개 변수를 사용합니다.

    | Value | 설명 | 예 |
    | --- | --- | --- |
    | 환경 이름 | 연결하려는 환경의 이름 | 이름 제공(예: `aze-environ`) |
    | Resource Manager 엔드포인트 | URL은 `https://Management.<appliancename><dnsdomain>`입니다. <br> 이 URL을 얻으려면 디바이스의 로컬 웹 UI에서 **디바이스** 페이지로 이동합니다. |예: `https://management.team3device.teatraining1.com`  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    다음 샘플은 위 명령의 사용법을 보여줍니다.
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. 다음 명령을 사용하여 활성 환경을 설정합니다.

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    다음 샘플은 위 명령의 사용법을 보여줍니다.

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. `az login` 명령을 사용하여 Azure Stack Edge 환경에 로그인합니다. 사용자나 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)로 Azure Stack Edge 환경에 로그인할 수 있습니다.

   *사용자*로 로그인하려면 다음 단계를 수행합니다.

   `az login` 명령 내에 직접 사용자 이름과 암호를 지정하거나 브라우저를 사용하여 인증할 수 있습니다. 계정에 다단계 인증을 사용하도록 설정된 경우 후자를 수행해야 합니다.

   다음 샘플은 `az login` 사용법을 보여줍니다.
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   로그인 명령을 사용하면 암호를 입력하라는 메시지가 표시됩니다. Azure Resource Manager 암호를 제공합니다.

   다음 샘플은 암호를 제공한 후 성공한 로그인의 출력을 보여줍니다.  
   
   ```powershell
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```

   서비스 주체로 작업하려면 다음 환경 변수를 설정해야 합니다.

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Azure Resource Manager 테넌트 ID, Azure Resource Manager 클라이언트 ID 및 Azure Resource Manager 구독 ID는 모두 하드코딩되며, 모든 Azure Stack Edge 디바이스에서 동일한 값을 갖습니다. Azure Resource Manager 클라이언트 암호는 내가 설정한 Azure Resource Manager 암호입니다.

   자세한 내용은 [Azure Resource Manager 암호](azure-stack-edge-j-series-set-azure-resource-manager-password.md)를 참조하세요.

5. 프로필을 2019-03-01-hybrid 버전으로 변경합니다. 프로필 버전을 변경하려면 다음 명령을 실행합니다.

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    다음 샘플은 `az cloud update` 사용법을 보여줍니다.

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>2단계: VM 만들기

VM을 생성하도록 Python 스크립트가 제공됩니다. 사용자로 로그인했는지 또는 서비스 주체로 설정되었는지에 따라 스크립트는 그에 따른 입력을 수행하여 VM을 만듭니다.

1. Python이 설치된 디렉터리에서 Python 스크립트를 실행합니다.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. 스크립트를 실행하면 VHD를 업로드하는 데 20~30분이 걸립니다. 업로드 작업의 진행률을 보려면 Azure Storage Explorer 또는 AzCopy를 사용하면 됩니다.

    다음은 스크립트 실행에 성공한 출력의 샘플입니다. 스크립트는 리소스 그룹 내의 모든 리소스를 만들고, 이러한 리소스를 사용하여 VM을 만든 다음, 마지막으로 생성된 모든 리소스를 포함한 리소스 그룹을 삭제합니다.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>다음 단계

[Linux 가상 머신용 일반적인 Az CLI 명령](../virtual-machines/linux/cli-manage.md)