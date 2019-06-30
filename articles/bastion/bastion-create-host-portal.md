---
title: Azure 요새 호스트 만들기 | Microsoft Docs
description: 이 문서에서는 Azure 요새 호스트를 만드는 방법에 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5fbd64d2f001a0fbe91de2d954889816a1819479
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339444"
---
# <a name="create-an-azure-bastion-host-preview"></a>(미리 보기)는 Azure 요새 호스트 만들기

이 문서에서는 Azure 요새 호스트를 만드는 방법을 보여 줍니다. 배스 천 Azure 서비스를 가상 네트워크에 프로 비전 하면 원활한 RDP/SSH 환경은 동일한 가상 네트워크에 있는 모든 Vm에 사용할 수 있습니다. 이 배포의 단위는 구독/계정 또는 가상 머신이 아니라 가상 네트워크입니다.

요새 호스트 리소스를 만들 수 있는 두 가지 있습니다.

* Azure portal을 사용 하 여 배스 천 리소스를 만듭니다.
* 기존 VM 설정을 사용 하 여 Azure portal에는 배스 천 리소스를 만듭니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

공개 미리 보기 Azure 공용 지역으로 제한 됩니다.

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

이 미리 보기에 참여 하려면 등록 해야 합니다. 미리 보기에 등록 하려면 다음이 단계를 사용 합니다.

[!INCLUDE [register](../../includes/bastion-preview-register-include.md)]

## <a name="createhost"></a>요새 호스트 만들기

이 섹션에서는 Azure portal에서 새 Azure 배스 천 리소스를 만들 수 있습니다.

1. 홈 페이지에서 [ Azure portal-미리 보기](https://aka.ms/BastionHost), 클릭 **+ 리소스 만들기**합니다. 이 미리 보기에 일반 Azure portal이 아닌 포털에 액세스 하려면 제공 된 링크를 사용 해야 합니다.

1. 에 **새로 만들기** 페이지를 *마켓플레이스를 검색 합니다* 필드에 입력 **배스 천**, 클릭 **Enter** 검색 결과를 가져오려고 합니다.

1. 결과에서 클릭 **배스 천 (미리 보기)** 합니다. 게시자가 있는지 *Microsoft* 범주 이며 *네트워킹*합니다.

1. 에 **배스 천 (미리 보기)** 페이지에서 **만들기** 열려는 합니다 **만들기는 배스 천** 페이지.

1. 에 **만들기를 배스 천** 페이지에서 새 배스 천 리소스를 구성 합니다. 배스 천 리소스에 대 한 구성 설정을 지정 합니다.

    ![배스 천 만들기](./media/bastion-create-host-portal/settings.png)

    * **구독**: 새 요새 리소스를 만드는 데 사용할 Azure 구독입니다.
    * **리소스 그룹**: Azure 리소스 그룹에 새 배스 천 리소스를 만들 수 있습니다. 기존 리소스 그룹에 없는 경우 새로 만들 수 있습니다.
    * **이름**: 새 요새 리소스의 이름
    * **지역**: 리소스에서 생성 될 Azure 공용 지역.
    * **가상 네트워크**: 가상 네트워크는 배스 천 리소스에서 생성 됩니다. 없는 또는 기존 가상 네트워크를 사용 하지 않으려는 경우이 과정에서 포털에서 새 가상 네트워크를 만들 수 있습니다. 기존 가상 네트워크를 사용 하는 경우 기존 가상 네트워크를 배스 천 서브넷 요구 사항에 맞게 충분 한 여유 주소 공간이 있는지 확인 합니다.
    * **서브넷**: 새 요새 호스트 리소스를 배포 하는 가상 네트워크의 서브넷입니다. 이름 값을 사용 하 여 서브넷을 만들어야 **AzureBastionSubnet**합니다. 이 값에는 Azure에는 배스 천 리소스를 배포 하는 서브넷을 알고 있습니다. 이 게이트웨이 서브넷을 다릅니다. 하나 이상의/27 또는 더 큰 서브넷을 사용 하는 것이 좋습니다 (/ 27, / 26, 및 등). 만들기는 **AzureBastionSubnet** 모든 네트워크 보안 그룹 없이 테이블 또는 위임 라우팅합니다.
    * **공용 IP 주소**: 공용 IP는 RDP/SSH (포트 443)을 통해 액세스 됩니다 배스 천 리소스입니다. 새 공용 IP를 만들거나 기존 계정을 사용 합니다. 공용 IP 주소를 만들면 배스 천 리소스와 동일한 지역에 있어야 합니다.
    * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다.
    * **공용 IP 주소 SKU**: 기본적으로 미리 채워져 **표준**합니다.
    * **할당**: 기본적으로 미리 채워져 **정적**합니다.

1. 설정을 지정 했으면 클릭 **검토 + 만들기**합니다. 이 값의 유효성을 검사 합니다. 유효성 검사에 통과 되 면 생성 프로세스를 시작할 수 있습니다.
1. 배스 천 페이지 만들기, 클릭 **만들기**합니다.
1. 알리는 메시지가 배포가 진행 되는 표시 됩니다. 상태는 리소스 생성 될 때이 페이지에 표시 됩니다. 배스 천 리소스 생성 및 배포에 대 한 약 5 분을 걸립니다.

## <a name="createvmset"></a>VM 설정을 사용 하 여 배스 천 호스트 만들기

포털에서 기존 VM을 사용 하 여 요새 호스트를 만든 경우 다양 한 설정을 기본값은 자동으로 가상 컴퓨터 및/또는 가상 네트워크에 해당 합니다.

1. 에 [미리 보기 포털](https://aka.ms/BastionHost)다음 가상 컴퓨터를 찾아 클릭 **Connect**합니다.

    ![VM 연결](./media/bastion-create-host-portal/vmsettings.png)

1. 오른쪽 사이드바에서 클릭 **배스 천**, 한 다음 **사용 하 여 배스 천**합니다.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. 배스 천 페이지에서 다음 설정 필드를 채웁니다.

    * **이름**: 만들려는 요새 호스트의 이름입니다.
    * **서브넷**: 배스 천 리소스를 배포할 가상 네트워크 내의 서브넷입니다. 이름을 사용 하 여 서브넷을 만들어야 **AzureBastionSubnet**합니다. 이렇게 하면 Azure는 배스 천 리소스를 배포할 서브넷을 알고 있습니다. 이 게이트웨이 서브넷을 다릅니다. 클릭 **관리 서브넷 구성을** Azure 배스 천 서브넷을 만들어야 합니다. 하나 이상의/27 또는 더 큰 서브넷을 사용 하는 것이 좋습니다 (/ 27, / 26 등.). 만들기는 **AzureBastionSubnet** 모든 네트워크 보안 그룹 없이 테이블 또는 위임 라우팅합니다. 클릭 **만들기** 서브넷을 만들려면 다음 설정을 사용 하 여 계속 합니다.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **공용 IP 주소**: 공용 IP는 RDP/SSH (포트 443)을 통해 액세스 됩니다 배스 천 리소스입니다. 새 공용 IP를 만들거나 기존 계정을 사용 합니다. 공용 IP 주소를 만들면 배스 천 리소스와 동일한 지역에 있어야 합니다.
    * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다.
1. 유효성 검사 화면에서 클릭 **만들기**합니다. 배스 천 리소스 생성 및 배포에 대 한 약 5 분 동안 기다립니다.

## <a name="next-steps"></a>다음 단계

읽기는 [배스 천 FAQ](bastion-faq.md)