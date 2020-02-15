---
title: 사용자 고유의 구독 Azure 이미지를 가져오는 Red Hat Enterprise Linux | Microsoft Docs
description: Azure에서 Red Hat Enterprise Linux에 대 한 사용자 고유의 구독 이미지 만들기에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: a54e1c9cbc9a80139d71a89f86ac51ecf5ce9902
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190510"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Azure에서 사용자 고유의 구독 골드 이미지를 가져오는 Red Hat Enterprise Linux

RHEL (Red Hat Enterprise Linux) 이미지는 PAYG (종 량 제) 또는 사용자 고유의 구독 (Red Hat 골드 이미지) 모델을 통해 Azure에서 사용할 수 있습니다. 이 문서에서는 Azure의 Red Hat 골드 이미지에 대 한 개요를 제공 합니다.

## <a name="important-points-to-consider"></a>고려해 야 할 중요 사항

- 이 프로그램에서 제공 하는 Red Hat 골드 이미지는 Azure 갤러리/Marketplace의 RHEL PAYG 이미지와 비슷한 프로덕션에 사용할 수 있는 RHEL 이미지입니다.

- 이미지는 [Azure의 Red Hat Enterprise Linux 이미지](./redhat-images.md) 에 설명 된 현재 정책을 따릅니다.

- 표준 지원 정책은 이러한 이미지에서 만든 Vm에 적용 됩니다.

- Red Hat 골드 이미지에서 프로 비전 된 Vm은 RHEL PAYG 이미지와 연결 된 RHEL 요금을 운반 하지 않습니다.

- 이미지의 자격이 부여 되지 않으므로, 구독 관리자를 사용 하 여 Red Hat에서 직접 업데이트를 가져오기 위해 Vm을 등록 하 고 구독 해야 합니다.

- 현재는 BYOS와 Linux 이미지에 대 한 PAYG 청구 모델 간을 동적으로 전환할 수 없습니다. 청구 모델을 전환 하려면 각 이미지에서 VM을 다시 배포 해야 합니다.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Red Hat 골드 이미지에 액세스 하기 위한 요구 사항 및 조건

1. Red hat [Cloud access 프로그램](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 용어에 대해 잘 알고 Red Hat [Subscription Manager](https://access.redhat.com/management/cloud)에서 클라우드 액세스에 대 한 red hat 구독을 사용 하도록 설정 하세요. 클라우드 액세스를 위해 등록 될 Azure 구독을 직접 보유 해야 합니다.

1. 적절 한 자격 요구 사항을 충족 하는 클라우드 액세스에 대 한 Red Hat 구독을 사용 하도록 설정한 경우 골드 이미지 액세스를 위해 Azure 구독을 자동으로 사용할 수 있습니다.

### <a name="expected-time-for-image-access"></a>이미지 액세스 예상 시간

클라우드 액세스 사용 단계가 완료 되 면 Red Hat는 Red Hat 골드 이미지에 대 한 자격의 유효성을 검사 합니다. 유효성 검사에 성공 하면 3 시간 이내에 골드 이미지에 대 한 액세스 권한을 받게 됩니다.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Azure Portal의 Red Hat 금색 이미지를 사용 합니다.

1. Red Hat 골드 이미지에 **대 한 액세스**를 받은 후 Azure 구독에서 **리소스 만들기** 로 이동 하 여 [Azure Portal](https://portal.azure.com) 에서 찾을 수 있습니다.

1. 페이지 맨 위에 개인 제안이 있는 것을 볼 수 있습니다.

    ![Marketplace 개인 제안](./media/rhel-byos-privateoffers.png)

1. 자주색 링크를 클릭 하거나 페이지 아래쪽으로 스크롤하여 개인 제품을 볼 수 있습니다.

1. UI에서의 나머지 부분은 기존 Red Hat 이미지와 다르지 않습니다. RHEL 버전을 선택 하 고 프롬프트에 따라 VM을 프로 비전 합니다. 이 프로세스를 통해 최종 단계에서 이미지의 조건에 동의할 수도 있습니다.

>[!NOTE]
>지금까지 이러한 단계를 통해 Red Hat 골드 이미지 이미지를 프로그래밍 방식으로 배포할 수 없습니다. 아래 "추가 정보" 섹션에 설명 된 대로 추가 단계가 필요 합니다.

이 문서의 나머지 부분에서는 CLI 메서드를 사용 하 여 이미지에서 용어를 프로 비전 하 고 사용 하는 방법에 중점을 둡니다. UI 및 CLI는 최종 결과 (프로 비전 된 RHEL 골드 이미지 VM)와 관련 하 여 완벽 하 게 교환할 수 있습니다.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Azure CLI의 Red Hat 금색 이미지를 사용 합니다.
다음 지침 집합에서는 Azure CLI 사용 하 여 RHEL VM에 대 한 초기 배포 프로세스를 안내 합니다. 이 지침에서는 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)되어 있다고 가정 합니다.

>[!IMPORTANT]
>다음 모든 명령에 대 한 게시자, 제품, 계획 및 이미지 참조의 모든 소문자를 사용 해야 합니다.

1. 원하는 구독에 있는지 확인 합니다.
    ```azurecli
    az account show -o=json
    ```

1. Red Hat 골드 이미지 VM에 대 한 리소스 그룹을 만듭니다.
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 이미지 사용 조건에 동의 합니다.
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >이러한 용어 *는 이미지 SKU 당 Azure 구독 당 한 번만*허용 해야 합니다.

1. 필드 다음 명령을 사용 하 여 VM 배포의 유효성을 검사 합니다.
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. `--validate` 인수 없이 위와 동일한 명령을 실행 하 여 VM을 프로 비전 합니다.
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. VM에 SSH를 적용 하 고 자격이 없는 이미지가 있는지 확인 합니다. 이렇게 하려면 `sudo yum repolist`를 실행 합니다 (RHEL 8의 `sudo dnf repolist`사용). 출력에는 구독 관리자를 사용 하 여 Red Hat에 VM을 등록 하 라는 메시지가 표시 됩니다.

>[!NOTE]
>RHEL 8 `dnf`와 `yum`는 서로 교환할 수 있습니다 .이에 대 한 자세한 내용은 [RHEL 8 관리자 가이드를 참조](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)하세요.


## <a name="use-the-red-hat-gold-images-from-powershell"></a>PowerShell에서 Red Hat 금색 이미지 사용
예제 스크립트는 다음과 같습니다. 리소스 그룹, 위치, VM 이름, 로그인 정보 및 기타 변수를 선택한 구성으로 바꾸어야 합니다. 게시자 및 계획 정보는 소문자 여야 합니다.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>사용자 고유의 구독 골드 이미지를 암호화 Red Hat Enterprise Linux

[Azure Disk Encryption](../../linux/disk-encryption-overview.md)를 사용 하 여 사용자 고유의 구독 골드 이미지를 보호할 수 Red Hat Enterprise Linux. 그러나 암호화를 사용 하도록 설정 하기 전에 구독을 등록 **해야** 합니다.  RHEL BYOS 골드 이미지 등록에 대 한 세부 정보는 Red Hat 사이트에서 사용할 수 있습니다. [Red Hat 구독 관리자를 사용 하 여 Red Hat 고객 포털에 시스템을 등록 및 구독 하는 방법을](https://access.redhat.com/solutions/253273)참조 하세요. 활성 Red Hat 구독이 있는 경우 [Red Hat 고객 포털 활성화 키 만들기](https://access.redhat.com/articles/1378093)를 읽을 수도 있습니다.

[Red Hat 사용자 지정 이미지](../../linux/redhat-create-upload-vhd.md)에서는 Azure Disk Encryption 지원 되지 않습니다. 추가 ADE 요구 사항 및 필수 구성 요소는 [Linux vm에 대 한 Azure Disk Encryption](../../linux/disk-encryption-overview.md#additional-vm-requirements)에 설명 되어 있습니다.

Azure Disk Encryption를 적용 하는 단계는 [Linux vm 및 관련 문서에서 Azure Disk Encryption 시나리오](../../linux/disk-encryption-linux.md) 에서 사용할 수 있습니다.

## <a name="additional-information"></a>추가 정보

- 이 제품에 대해 사용 하도록 설정 되지 않은 구독에서 VM을 프로 비전 하려고 하면 다음과 같은 오류가 표시 됩니다.

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    이 경우 Microsoft 또는 Red Hat에 연락 하 여 구독을 사용 하도록 설정 합니다.

- RHEL BYOS 이미지에서 스냅숏을 수정 하 고이 사용자 지정 이미지를 [공유 이미지 갤러리](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)에 게시 하려는 경우 스냅숏의 원래 원본과 일치 하는 계획 정보를 제공 해야 합니다. 예를 들어 명령은 다음과 같습니다.

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    위의 마지막 줄에서 계획 매개 변수를 확인 합니다.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) 은 사용자 지정 이미지에서 지원 되지 않습니다.

- Automation을 사용 하 여 RHEL BYOS 이미지에서 Vm을 프로 비전 하는 경우 위에 표시 된 것과 유사한 계획 매개 변수를 제공 해야 합니다. 예를 들어 Terraform을 사용 하는 경우 계획 [블록](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)에 계획 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계
- 클라우드 액세스에 대 한 단계별 가이드 및 프로그램 세부 정보는 [Red Hat Cloud access 설명서](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) 에서 확인할 수 있습니다.
- [Azure Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아보세요.
- Azure의 모든 Red Hat 이미지에 대해 자세히 알아보려면 [설명서 페이지로](./redhat-images.md)이동 합니다.
- 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
- RHEL 골드 이미지에 대 한 추가 설명서는 [Red Hat 설명서](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)에서 찾을 수 있습니다.
