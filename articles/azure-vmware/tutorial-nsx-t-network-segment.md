---
title: '자습서: Azure VMware Solution에서 NSX-T 네트워크 세그먼트 만들기'
description: 이 자습서에서는 vCenter의 VM에 사용되는 NSX-T 네트워크 세그먼트를 만듭니다.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: cee65211cbef25ec029c68888bc8e6059f7c7896
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750472"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>자습서: Azure VMware Solution에서 NSX-T 네트워크 세그먼트 만들기

NSX-T Manager에서 만든 네트워크 세그먼트는 vCenter의 VM(가상 머신)에 대한 네트워크로 사용됩니다. vCenter에서 만든 VM은 NSX-T에서 만든 네트워크 세그먼트에 배치되고 vCenter에 표시됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * NSX-T Manager에서 탐색하여 네트워크 세그먼트 추가
> * 새 네트워크 세그먼트 추가
> * vCenter의 새 네트워크 세그먼트 관찰

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 vCenter 및 NSX-T Manager 관리 인터페이스에 액세스할 수 있는 Azure VMware Solution 프라이빗 클라우드가 필요합니다. [자습서: Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성](tutorial-configure-networking.md)를 참조하세요.

## <a name="provision-a-network-segment-in-nsx-t"></a>NSX-T에서 네트워크 세그먼트 프로비저닝

1. 프라이빗 클라우드의 vCenter에서 **SDDC-Datacenter > 네트워크**를 선택하고 아직 네트워크가 없는지 확인합니다.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. 프라이빗 클라우드의 NSX-T Manager에서 **네트워킹**을 선택합니다.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="프라이빗 클라우드의 NSX-T Manager에서 네트워킹을 선택합니다.":::

1. **세그먼트**를 선택합니다.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="네트워크 개요 페이지에서 세그먼트를 선택합니다.":::

1. NSX-T 세그먼트 개요 페이지에서 **세그먼트 추가**를 선택합니다. 프라이빗 클라우드 프로비저닝의 일부로 세그먼트 세 개가 생성되며 VM에는 사용할 수 없습니다.  이 목적을 위해서는 새 네트워크 세그먼트를 추가해야 합니다.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="NSX-T 세그먼트 개요 페이지에서 세그먼트 추가를 선택합니다.":::

1. 세그먼트 이름을 지정하고 미리 구성된 Tier1 게이트웨이(TNTxx-T1)를 **연결된 게이트웨이**로 선택하고, **유형**은 [유동적]으로 유지하고, 미리 구성된 오버레이 **전송 영역**(TNTxx-OVERLAY-TZ)을 선택한 다음, [서브넷 설정]을 선택합니다. 이 섹션의 다른 모든 설정과 **포트** 및 **세그먼트 프로파일**은 구성 그대로 기본값으로 유지하면 됩니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="세그먼트 이름, 연결된 게이트웨이 및 유형, 전송 영역을 설정한 다음, 서브넷 설정을 선택합니다.":::

1. 새 세그먼트에 대한 게이트웨이의 IP 주소를 설정한 다음, **추가**를 선택합니다. 사용하는 IP 주소는 겹치지 않는 RFC1918 주소 블록에 있어야 합니다. 그래야 새 세그먼트의 VM에 연결할 수 있습니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="새 세그먼트에 대한 게이트웨이의 IP 주소를 설정한 다음, 추가를 선택합니다.":::

1. **적용**을 선택하여 새 네트워크 세그먼트를 적용한 다음, **저장**을 사용하여 구성을 저장합니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="[적용]을 사용하여 새 네트워크 세그먼트를 NSX-T 구성에 적용합니다.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="[저장]을 사용하여 새 네트워크 세그먼트를 NSX-T 구성에 저장합니다.":::

1. 이제 새 네트워크 세그먼트가 생성되었고 **아니요**를 선택하여 세그먼트 구성을 계속하는 옵션을 거부합니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="[아니요]를 선택하여 새로 만든 네트워크 세그먼트를 추가적으로 구성하는 것을 거부합니다.":::

1. **네트워킹 > 세그먼트**를 선택하고 새 세그먼트가 목록에 있는지 확인하여(이 경우 "ls01") NSX에 새 네트워크 세그먼트가 있는지 확인합니다.

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="NSX에 새 네트워크 세그먼트가 있는지 확인합니다.":::

1. **네트워킹 > SDDC-Datacenter**를 선택하고 새 세그먼트가 목록에 있는지 확인하여(이 경우 "ls01") vCenter에 새 네트워크 세그먼트가 있는지 확인합니다.

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="vCenter에 새 네트워크 세그먼트가 있는지 확인합니다.":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 vCenter의 VM에 사용되는 NSX-T 네트워크 세그먼트를 만들었습니다. 이제 [자습서: Azure VMware Solution에서 VM을 배포하는 콘텐츠 라이브러리 만들기](tutorial-deploy-vm-content-library.md)를 사용하여 vCenter에서 콘텐츠 라이브러리를 만들고 이 자습서에서 만든 네트워크에서 VM을 프로비저닝할 수 있습니다.

<!-- LINKS - external-->

<!-- LINKS - internal -->
