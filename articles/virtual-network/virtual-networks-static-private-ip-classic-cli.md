---
title: VM(클래식)에 대한 사설 IP 주소 구성 - Azure 클래식 CLI
titlesuffix: Azure Virtual Network
description: Azure 클래식 CLI(명령줄 인터페이스)를 사용하여 가상 머신(클래식)에 대한 개인 IP 주소를 구성하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 2f71dc2bcd5463f81ae286bbe3099124eb3fa539
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107981"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Azure 클래식 CLI를 사용하여 가상 머신(클래식)에 대한 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-arm-cli.md)할 수도 있습니다.

다음 샘플 Azure 클래식 CLI 명령에는 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [vnet 만들기](virtual-networks-create-vnet-classic-cli.md)에 설명된 테스트 환경을 구축합니다.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM을 만들 때 정적 개인 IP 주소를 지정하는 방법
위의 시나리오를 기반으로 *TestService*라는 새 클라우드 서비스에 *DNS01*이라는 VM을 만들려면 다음 단계를 수행합니다.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](/cli/azure/install-cli-version-1.0)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. **azure service create** 명령을 실행하여 클라우드 서비스를 만듭니다.
   
        azure service create TestService --location uscentral
   
    예상 출력:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. **azure create vm** 명령을 실행하여 VM을 만듭니다. 정적 개인 IP 주소에 대한 값을 확인합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    예상 출력:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l(또는 --location)**. VM을 만들 Azure 지역입니다. 이 시나리오에서는 *centralus*입니다.
   * **-n(또는 --vm-name)**. 만들 VM의 이름입니다.
   * **-w(또는 --virtual-network-name)**. VM이 만들어지는 VNet의 이름입니다. 
   * **-S(또는 --static-ip)**. VM에 대한 정적 개인 IP 주소입니다.
   * **TestService**. VM이 만들어지는 클라우드 서비스의 이름입니다.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. VM을 만드는 데 사용한 이미지입니다.
   * **adminuser**. Windows VM에 대한 로컬 관리자입니다.
   * <strong>AdminP@ssw0rd</strong>. Windows VM에 대한 로컬 관리자 암호입니다.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보를 검색하는 방법
위의 스크립트로 만든 VM에 대한 정적 개인 IP 주소 정보를 보려면 다음 Azure CLI 명령을 실행하고 *Network StaticIP*에 대한 값을 확인합니다.

    azure vm static-ip show DNS01

예상 출력:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소를 제거하는 방법
위의 스크립트에서 VM에 추가된 정적 개인 IP 주소를 제거하려면 다음 Azure CLI 명령을 실행합니다.

    azure vm static-ip remove DNS01

예상 출력:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>기존 VM에 정적 개인 IP를 추가하는 방법
위의 스크립트를 사용하여 만든 VM에 정적 사설 IP 주소를 추가하려면 다음 명령을 실행합니다.

    azure vm static-ip set DNS01 192.168.1.101

예상 출력:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure VM에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. 가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다.

## <a name="next-steps"></a>다음 단계
* [예약된 공용 IP](virtual-networks-reserved-public-ip.md) 주소에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
* [예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 참조합니다.