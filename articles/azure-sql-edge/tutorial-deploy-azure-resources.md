---
title: Azure SQL Edge에서 ML 모델 배포를 위한 리소스 설정
description: 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 Azure SQL Edge 자습서 중 1부에서는 필수 구성 요소 소프트웨어를 설치하고 Azure SQL Edge에서 기계 학습 모델을 배포하는 데 필요한 Azure 리소스를 설정합니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 76c45e586ea7101015cb878d198cab73ed32498e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018249"
---
# <a name="install-software-and-set-up-resources-for-the-tutorial"></a>자습서를 위한 소프트웨어 설치 및 리소스 설정

세 부분으로 구성된 이 자습서에서는 실리카의 비율로 철광석 불순물을 예측하는 기계 학습 모델을 만든 다음, Azure SQL Edge에 모델을 배포합니다. 1부에서는 필요한 소프트웨어를 설치하고 Azure 리소스를 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

1. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
2. [Python 3.6.8](https://www.python.org/downloads/release/python-368/)을 설치합니다.
      * Windows x86-x64 실행 파일 설치 관리자 사용
      * PATH 환경 변수 다운로드에 `python.exe`를 추가 “Visual Studio 2019용 도구”에서 다운로드를 찾을 수 있습니다.
3. [Microsoft ODBC Driver 17 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)를 설치합니다.
4. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio/)를 설치합니다.
5. Azure Data Studio를 열고 Notebook에 대해 Python을 구성합니다. 자세한 내용은 [Notebook용 Python 구성](/sql/azure-data-studio/sql-notebooks#configure-python-for-notebooks)을 참조하세요. 이 단계는 몇 분 정도 걸릴 수 있습니다.
6. 최신 버전의 [Azure CLI](https://github.com/Azure/azure-powershell/releases/tag/v3.5.0-February2020)를 설치합니다. 다음 스크립트는 최신 버전(3.5.0, 2020년 2월)의 AZ PowerShell이 필요합니다.
7. 자습서에 사용할 [DACPAC](https://github.com/microsoft/sql-server-samples/tree/master/samples/demos/azure-sql-edge-demos/iron-ore-silica-impurities/DACPAC) 및 [AMD/ARM Docker 이미지 파일](https://www.docker.com/blog/multi-arch-images/)을 다운로드합니다.

## <a name="deploy-azure-resources-using-powershell-script"></a>PowerShell 스크립트를 사용하여 Azure 리소스 배포

이 Azure SQL Edge 자습서에 필요한 Azure 리소스를 배포합니다. PowerShell 스크립트를 사용하거나 Azure Portal을 통해 배포할 수 있습니다. 이 자습서에서는 PowerShell 스크립트를 사용합니다.

1. 이 자습서에서 PowerShell 스크립트를 실행하는 데 필요한 모듈을 가져옵니다.

   ```powershell
   Import-Module Az.Accounts -RequiredVersion 1.7.3
   Import-Module -Name Az -RequiredVersion 3.5.0
   Import-Module Az.IotHub -RequiredVersion 2.1.0
   Import-Module Az.Compute -RequiredVersion 3.5.0
   az extension add --name azure-iot
   az extension add --name azure-cli-ml
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]


2. PowerShell 스크립트에 필요한 변수를 선언합니다.

   ```powershell
   $ResourceGroup = "<name_of_the_resource_group>"
   $IoTHubName = "<name_of_the_IoT_hub>"
   $location = "<location_of_your_Azure_Subscription>"
   $SubscriptionName = "<your_azure_subscription>"
   $NetworkSecGroup = "<name_of_your_network_security_group>"
   $StorageAccountName = "<name_of_your_storage_account>"
   ```

3. 나머지 변수를 선언합니다.

   ```powershell
   $IoTHubSkuName = "S1"
   $IoTHubUnits = 4
   $EdgeDeviceId = "IronOrePredictionDevice"
   $publicIpName = "VMPublicIP"
   $imageOffer = "iot_edge_vm_ubuntu"
   $imagePublisher = "microsoft_iot_edge"
   $imageSku = "ubuntu_1604_edgeruntimeonly"
   $AdminAcc = "iotadmin"
   $AdminPassword = ConvertTo-SecureString "IoTAdmin@1234" -AsPlainText -Force
   $VMSize = "Standard_DS3"
   $NetworkName = "MyNet"
   $NICName = "MyNIC"
   $SubnetName = "MySubnet"
   $SubnetAddressPrefix = "10.0.0.0/24"
   $VnetAddressPrefix = "10.0.0.0/16"
   $MyWorkSpace = "SQLDatabaseEdgeDemo"
   $containerRegistryName = $ResourceGroup + "ContRegistry"
   ```

4. 자산 만들기를 시작하려면 Azure에 로그인합니다.

   ```powershell
   Login-AzAccount
   
   az login
   ```

5. Azure 구독 ID를 설정합니다.

   ```powershell
   Select-AzSubscription -Subscription $SubscriptionName
   az account set --subscription $SubscriptionName
   ```

6. 리소스 그룹이 없는 경우 생성합니다.

   ```powershell
   $rg = Get-AzResourceGroup -Name $ResourceGroup
   if($rg -eq $null)
   {
       Write-Output("Resource Group $ResourceGroup does not exist, creating Resource Gorup")
       New-AzResourceGroup -Name $ResourceGroup -Location $location
   }
   else
   {
       Write-Output ("Resource Group $ResourceGroup exists")
   }
   ```

7. 리소스 그룹에 스토리지 계정 및 스토리지 계정 컨테이너를 만듭니다.

   ```powershell
   $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
   if ($sa -eq $null)
   {
       New-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -SkuName Standard_LRS -Location $location -Kind Storage
       $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storagekey = Get-AzStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $storagekey[0].Value
       New-AzStorageContainer -Name "sqldatabasedacpac" -Context $storageContext 
   }
   else
   {
      Write-Output ("Storage Account $StorageAccountName exists in Resource Group $ResourceGroup")     
   }
   ```

8. 데이터베이스 `dacpac` 파일을 스토리지 계정에 업로드하고 Blob에 대한 SAS URL을 생성합니다. **데이터베이스 `dacpac` Blob에 대한 SAS URL을 적어 둡니다.**

   ```powershell
   $file = Read-Host "Please Enter the location to the zipped Database DacPac file:"
   Set-AzStorageBlobContent -File $file -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context
   $DacpacFileSASURL = New-AzStorageBlobSASToken -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context -Permission r -StartTime (Get-Date).DateTime -ExpiryTime (Get-Date).AddMonths(12) -FullUri
   ```

9. 이 리소스 그룹 내에 Azure Container Registry를 만듭니다.

   ```powershell
   $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   if ($containerRegistry -eq $null)
   {
       New-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName -Sku Standard -Location $location -EnableAdminUser 
       $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   }
   else
   {
       Write-Output ("Container Registry $containerRegistryName exists in Resource Group $ResourceGroup")
   }
   ```

10. ARM/AMD Docker 이미지를 컨테이너 레지스트리로 푸시합니다.

    ```powershell
    $containerRegistryCredentials = Get-AzContainerRegistryCredential -ResourceGroupName $ResourceGroup -Name $containerRegistryName
    
    $amddockerimageFile = Read-Host "Please Enter the location to the amd docker tar file:"
    $armdockerimageFile = Read-Host "Please Enter the location to the arm docker tar file:"
    $amddockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":amd64"
    $armdockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":arm64"
    
    docker login $containerRegistry.LoginServer --username $containerRegistryCredentials.Username --password $containerRegistryCredentials.Password
    
    docker import $amddockerimageFile $amddockertag
    docker push $amddockertag
    
    docker import $armdockerimageFile $armdockertag
    docker push $armdockertag
    ```

11. 리소스 그룹 내에 네트워크 보안 그룹을 만듭니다.

    ```powershell
    $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup 
    if($nsg -eq $null)
    {
        Write-Output("Network Security Group $NetworkSecGroup does not exist in the resource group $ResourceGroup")
    
        $rule1 = New-AzNetworkSecurityRuleConfig -Name "SSH" -Description "Allow SSH" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22
        $rule2 = New-AzNetworkSecurityRuleConfig -Name "SQL" -Description "Allow SQL" -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1600
        New-AzNetworkSecurityGroup -Name $NetworkSecGroup -ResourceGroupName $ResourceGroup -Location $location -SecurityRules $rule1, $rule2
    
        $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup
    }
    else
    {
        Write-Output ("Network Security Group $NetworkSecGroup exists in the resource group $ResourceGroup")
    }
    ```

12. SQL Edge에서 사용하도록 설정된 Azure 가상 머신을 만듭니다. 이 VM은 에지 디바이스 역할을 합니다.

    ```powershell
    $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
    If($AzVM -eq $null)
    {
        Write-Output("The Azure VM with Name- $EdgeVMName is not present in the Resource Group- $ResourceGroup ")
    
        $SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
        $Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroup -Location $location -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
        $publicIp = New-AzPublicIpAddress -Name $publicIpName -ResourceGroupName $ResourceGroup -AllocationMethod Static -Location $location  
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $publicIp.Id
         
        ##Set-AzNetworkInterfaceIpConfig -Name "ipconfig1"  -NetworkInterface $NIC -PublicIpAddress $publicIp
    
        $Credential = New-Object System.Management.Automation.PSCredential ($AdminAcc, $AdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $EdgeDeviceId -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $EdgeDeviceId -Credential $Credential
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id  
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $imagePublisher -Offer $imageOffer -Skus $imageSku -Version latest 
        $VirtualMachine = Set-AzVMPlan -VM $VirtualMachine -Name $imageSku -Publisher $imagePublisher -Product $imageOffer
    
        $AzVM = New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $VirtualMachine -Verbose
        $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
          
    }
    else
    {
        Write-Output ("The Azure VM with Name- $EdgeDeviceId is present in the Resource Group- $ResourceGroup ")
    }
    ```

13. 리소스 그룹 내에 IoT 허브를 만듭니다.

    ```powershell
    $iotHub = Get-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName
    If($iotHub -eq $null)
    {
        Write-Output("IoTHub $IoTHubName does not exists, creating The IoTHub in the resource group $ResourceGroup")
        New-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName -SkuName $IoTHubSkuName -Units $IoTHubUnits -Location $location -Verbose
    }
    else
    {
        Write-Output ("IoTHub $IoTHubName present in the resource group $ResourceGroup") 
    }
    ```

14. 에지 디바이스를 IoT 허브에 추가합니다. 이 단계에서는 디바이스 디지털 ID만 만듭니다.

    ```powershell
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    If($deviceIdentity -eq $null)
    {
        Write-Output("The Edge Device with DeviceId- $EdgeDeviceId is not registered to the IoTHub- $IoTHubName ")
        Add-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId -EdgeEnabled  
    }
    else
    {
        Write-Output ("The Edge Device with DeviceId- $EdgeDeviceId is registered to the IoTHub- $IoTHubName")
    }
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    ```

15. 디바이스 기본 연결 문자열을 가져옵니다. 이는 나중에 VM에 필요합니다. 다음 명령은 배포에 Azure CLI를 사용합니다.

    ```powershell
    $deviceConnectionString = az iot hub device-identity show-connection-string --device-id $EdgeDeviceId --hub-name $IoTHubName --resource-group $ResourceGroup --subscription $SubscriptionName
    $connString = $deviceConnectionString[1].Substring(23,$deviceConnectionString[1].Length-24)
    $connString
    ```

16. 에지 디바이스의 IoT Edge 구성 파일에서 연결 문자열을 업데이트합니다. 다음 명령은 배포에 Azure CLI를 사용합니다.

    ```powershell
    $script = "/etc/iotedge/configedge.sh '" + $connString + "'"
    az vm run-command invoke -g $ResourceGroup -n $EdgeDeviceId  --command-id RunShellScript --script $script
    ```

17. 리소스 그룹 내에 Azure Machine Learning 작업 영역을 만듭니다.

    ```powershell
    az ml workspace create -w $MyWorkSpace -g $ResourceGroup
    ```

## <a name="next-steps"></a>다음 단계

* [IoT Edge 모듈 및 연결 설정](tutorial-set-up-iot-edge-modules.md)
