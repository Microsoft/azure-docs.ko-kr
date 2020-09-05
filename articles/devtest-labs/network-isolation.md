---
title: Azure DevTest Labs의 네트워크 격리
description: Azure DevTest Labs의 네트워크 격리에 대해 알아봅니다.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: fbd2725cd3677e03cadbe0ae1f060b141f5d212b
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875963"
---
# <a name="network-isolation-in-devtest-labs"></a>DevTest Labs의 네트워크 격리

[Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md) 는 보안 경계 역할을 하 여 공용 인터넷에서 azure 리소스를 격리 합니다. Azure 가상 네트워크를 온-프레미스 네트워크에 가입 하 여 온-프레미스 리소스에 안전 하 게 연결할 수도 있습니다. DevTest Labs에서 랩 리소스가 조직 네트워킹 정책을 따르도록 하기 위해 [모든 랩 가상 컴퓨터](devtest-lab-configure-vnet.md) 및 [환경을 네트워크에](connect-environment-lab-virtual-network.md) 격리 하도록 선택할 수 있습니다. 

랩 소유자는 랩을 완전히 격리 하도록 선택할 수도 있습니다. 즉, 가상 컴퓨터와 환경을 선택한 네트워크로 격리 하는 것 처럼 랩 저장소 계정 및 구독에서 생성 된 키 자격 증명 모음을 격리할 수도 있습니다. 이 문서에서는 네트워크 격리 랩을 만드는 과정을 안내 합니다. 

또한 다음 문서를 검토 합니다.

- [DevTest Labs에서 랩 저장소 계정을 사용 하는 방법](encrypt-storage.md)
- [DevTest Labs에서 주요 자격 증명 모음을 사용 하는 방법](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> 네트워크 격리는 현재 새 랩 만들기에만 지원 됩니다.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>랩을 만드는 동안 네트워크 격리를 사용 하도록 설정 하는 단계

1. 랩 생성 중에 **네트워킹** 탭으로 이동 합니다.
1. 랩에서 만들 **기본** 네트워크를 선택 하거나 드롭다운에서 기존 네트워크를 선택할 수 있습니다. 랩의 경우와 동일한 지역 및 구독에 있는 네트워크를 선택할 수 있습니다. 

    > [!div class="mx-imgBorder"]
    > ![랩 만들기](./media/network-isolation/create-lab.png)
1. 서브넷을 선택 하십시오.

    > [!div class="mx-imgBorder"]
    > ![서브넷 만들기](./media/network-isolation/create-lab-subnet.png)
1. 랩 리소스 (lab storage 계정 및 키 자격 증명 모음)를 기본 네트워크로 격리 하도록 선택 하면 추가 작업이 필요 하지 않으며 랩에서 앞으로의 리소스 격리를 처리 합니다.
 
    > [!div class="mx-imgBorder"]
    > ![네트워크 격리](./media/network-isolation/isolate-lab-resources.png)
1. 랩 리소스 (랩 저장소 계정 및 키 자격 증명 모음)를 선택한 기존 네트워크로 격리 하도록 선택 하는 경우 랩 생성 후 다음 단계를 완료 하 여 랩이 격리 모드에서 계속 작동 하는지 확인 해야 합니다. 
 
    > [!div class="mx-imgBorder"]
    > ![자원 배정 격리](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > 랩 소유자는 랩에서 리소스를 구성 및/또는 만들기 전에 이러한 단계를 완료 해야 합니다.

### <a name="steps-to-follow-post-lab-creation"></a>랩 생성 후에 수행 하는 단계

1. 랩의 홈 페이지에 있는 **개요** 페이지에서 **리소스 그룹** 을 선택 합니다. 랩을 포함 하는 리소스 그룹에 대 한 **리소스 그룹** 페이지가 표시 되어야 합니다. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 랩](./media/network-isolation/contoso-lab.png)
1. 랩의 Azure storage 계정을 선택 합니다. 랩 저장소 계정에 대 한 명명 규칙은<*labNameWithoutInvalidCharacters* > *<4 자리 숫자*>입니다. 예를 들어 랩 이름이 contosolab 인 경우 저장소 계정 이름은 acontosolab1234 수 있습니다.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 테스트](./media/network-isolation/contoso-test.png)
1. 저장소 계정에서 방화벽 및 가상 네트워크로 이동 하 고 ' 신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용 ' 확인란을 선택 했는지 확인 합니다. [DevTest Labs는 신뢰할 수 있는 Microsoft 서비스 이므로](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)이 옵션을 사용 하면 랩을 네트워크 격리 모드에서 정상적으로 작동할 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![Contoso lab 방화벽](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 그런 다음, **기존 가상 네트워크 추가**를 클릭 하 고 랩을 만들 때 선택한 가상 네트워크 및 서브넷을 선택 하 고 **사용**을 클릭 합니다. 

   > [!div class="mx-imgBorder"]
   > ![Contoso 내 vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  선택한 가상 네트워크에 대해 서비스 끝점이 성공적으로 설정 되 면 **추가**를 클릭 합니다. 

   > [!div class="mx-imgBorder"]
   > ![추가](./media/network-isolation/contoso-firewall-add.png)
 
이를 통해 Azure storage는 추가 된 가상 네트워크에서 인바운드 연결을 허용 하 고 랩을 네트워크 격리 모드에서 성공적으로 작동할 수 있도록 합니다. 

이러한 단계를 자동화 하 여 여러 랩에 대해이 설정을 구성 하도록 선택할 수도 있습니다. 

[PowerShell 및 CLI를 사용 하 여 Azure Storage에 대 한 기본 네트워크 액세스 규칙 관리에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=/azure/virtual-network/toc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>네트워크 격리 모드에서 랩을 사용 하는 동안 기억할 사항

### <a name="accessing-labs-storage-account-outside-the-lab"></a>랩 외부 랩의 저장소 계정 액세스 

네트워크 격리 랩 내에서 랩의 저장소 계정에 VHD를 업로드 하 여에서 사용자 지정 이미지를 만드는 등의 작업을 위해 랩 소유자는 허용 된 끝점에서 저장소 계정에 대 한 액세스를 명시적으로 사용 하도록 설정 해야 합니다. 가상 컴퓨터를 만들고 해당 가상 컴퓨터에서 랩의 저장소 계정에 안전 하 게 액세스 하 여이 작업을 수행할 수 있습니다. 

[가상 머신에서 개인적으로 저장소 계정에 액세스 하는 방법에 대해 자세히 알아보세요.](../private-link/create-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>랩에서 사용 현황 데이터 내보내기 

네트워크 격리 랩 내에서 [랩에 대 한 개인 사용 데이터를 내보내려면](personal-data-delete-export.md)랩 소유자는 명시적으로 저장소 계정을 제공 하 고 계정 내에서 데이터를 저장할 blob을 생성 해야 합니다. 

저장소 계정이 제공 되지 않은 경우 랩의 저장소 계정에 액세스할 수 없기 때문에이 작업은 네트워크 격리 모드에서 실패 합니다 .이는 랩에서 고객이 제공한 저장소 계정이 없는 경우에 사용할 수 있습니다. 

[지정 된 저장소 계정에서 랩 사용 데이터 내보내기에 대 한 자세한 정보](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager 템플릿 및 PowerShell을 사용하여 랩 자동 생성 또는 수정](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
