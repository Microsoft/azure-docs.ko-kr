---
title: Azure Batch 계정으로 프라이빗 엔드포인트 사용
description: 개인 끝점을 사용 하 여 Azure Batch 계정에 비공개로 연결 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 08/07/2020
ms.custom: references_regions
ms.openlocfilehash: 0fd16e4e11d0b3f08a7ba0e2f425785e3cce7927
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814113"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Azure Batch 계정으로 프라이빗 엔드포인트 사용

기본적으로 [Azure Batch 계정은](accounts.md) 공용 끝점을 포함 하며 공개적으로 액세스할 수 있습니다. Batch 서비스는 개인 Batch 계정을 만들고 공용 네트워크 액세스를 사용 하지 않도록 설정 하는 기능을 제공 합니다.

[Azure 개인 링크](../private-link/private-link-overview.md)를 사용 하 여 [개인 끝점](../private-link/private-endpoint-overview.md)을 통해 Azure Batch 계정에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내부에 있는 서브넷의 개인 IP 주소 세트입니다. 그런 다음 개인 IP 주소를 통해 Azure Batch 계정에 대 한 액세스를 제한할 수 있습니다.

개인 링크를 사용 하면 사용자가 가상 네트워크 또는 피어 링 가상 네트워크 내에서 Azure Batch 계정에 액세스할 수 있습니다. 개인 링크에 매핑된 리소스는 VPN 또는 [Azure express](../expressroute/expressroute-introduction.md)경로를 통해 개인 피어 링을 통해 온-프레미스 에서도 액세스할 수 있습니다. [자동 또는 수동 승인 방법을](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)사용 하 여 개인 링크로 구성 된 Azure Batch 계정에 연결할 수 있습니다.

> [!IMPORTANT]
> Azure Batch의 개인 연결에 대 한 지원은 현재 미국 중부, 미국 중 북부, 미국 중 북부, 미국 서 부, 미국 서 부, 미국 동부 2, 미국 서 부, 미국 서 부 2, 동아시아, 프랑스 중부, 영국 남부, 유럽 서부 유럽 서 부, 일본 동부, 일본 서 부, 오스트레일리아 동부, 모든 US Gov 및 US DoD 지역에서 사용할 수 있습니다.

이 문서에서는 개인 일괄 처리 계정을 만들고 개인 끝점을 사용 하 여 액세스 하는 단계를 설명 합니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal를 사용 하 여 개인 배치 계정을 만들려면 다음 단계를 수행 합니다.

1. **리소스 만들기** 창에서 **Batch 서비스** 를 선택한 다음 **만들기**를 선택 합니다.
2. **기본 사항** 탭에 구독, 리소스 그룹, 지역 및 배치 계정 이름을 입력 하 고 **다음: 고급**을 선택 합니다.
3. **고급** 탭에서 **공용 네트워크 액세스** 를 **사용 안 함**으로 설정 합니다.
4. **설정**에서 **개인 끝점 연결** 을 선택 하 고 **+ 개인 끝점**을 선택 합니다.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="개인 끝점 연결":::
5. **기본 사항** 창에서 구독, 리소스 그룹, 개인 끝점 리소스 이름 및 지역 세부 정보를 입력 하거나 선택 하 고 **다음: 리소스**를 선택 합니다.
6. **리소스** 창에서 **리소스 종류** 를 **ch/batchaccountsMicrosoft.Bat**로 설정 합니다. 액세스 하려는 개인 Batch 계정을 선택 하 고 **다음: 구성**을 선택 합니다.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="개인 끝점 만들기-리소스 창":::
7. **구성** 창에서 다음 정보를 입력 하거나 선택 합니다.
   - **가상 네트워크**: 가상 네트워크를 선택 합니다.
   - **서브넷**: 서브넷을 선택 합니다.
   - **개인 DNS 영역과 통합**: **예**를 선택 합니다. 프라이빗 엔드포인트에 비공개로 연결하려면 DNS 레코드가 필요합니다. 프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합하는 것이 좋습니다. 자체 DNS 서버를 활용하거나 가상 머신의 호스트 파일을 사용하여 DNS 레코드를 만들 수도 있습니다.
   - **사설 DNS 영역**: <region> privatelink를 선택 합니다. batch.azure.com. 프라이빗 DNS 영역은 자동으로 결정됩니다. Azure Portal을 사용하여 변경할 수 없습니다.
8. **검토 + 만들기**를 선택한 다음 Azure에서 구성의 유효성을 검사할 때까지 기다립니다.
9. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

개인 끝점이 프로 비전 되 면 개인 끝점을 사용 하 여 동일한 가상 네트워크의 Vm에서 Batch 계정에 액세스할 수 있습니다. Azure Portal에서 IP 주소를 보려면 다음을 수행 합니다.

1. **모든 리소스**를 선택합니다.
2. 이전에 만든 프라이빗 엔드포인트를 검색합니다.
3. **개요** 탭을 선택하여 DNS 설정 및 IP 주소를 확인합니다.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="개인 끝점 DNS 설정 및 IP 주소":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

[Azure Resource Manager 템플릿을 사용 하 여 Batch 계정을 만들](quick-create-template.md)때 아래와 같이 **Publicnetworkaccess** 를 **사용 안 함으로** 설정 하도록 템플릿을 수정 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>DNS 영역 구성

개인 끝점을 만든 서브넷 내에서 [개인 DNS 영역](../dns/private-dns-privatednszone.md) 을 사용 합니다. 각 개인 IP 주소가 DNS 항목에 매핑되도록 엔드포인트를 구성합니다.

개인 끝점을 만들 때 Azure의 [개인 DNS 영역과](../dns/private-dns-privatednszone.md) 통합할 수 있습니다. 대신 [사용자 지정 도메인](../dns/dns-custom-domain.md)을 사용 하도록 선택 하는 경우 개인 끝점에 예약 된 모든 개인 IP 주소에 대 한 DNS 레코드를 추가 하도록 구성 해야 합니다.

## <a name="pricing"></a>가격 책정

개인 끝점과 관련 된 비용에 대 한 자세한 내용은 [Azure 개인 링크 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조 하세요.

## <a name="current-limitations-and-best-practices"></a>현재 제한 사항 및 모범 사례

개인 Batch 계정을 만들 때 다음 사항에 유의 하세요.

- 개인 끝점 리소스는 Batch 계정과 동일한 구독에 만들어야 합니다.
- 개인 연결을 삭제 하려면 개인 끝점 리소스를 삭제 해야 합니다.
- Batch 계정이 공용 네트워크 액세스를 사용 하 여 만들어진 후에는 개인 액세스로만 변경할 수 없습니다.
- 개인 DNS 영역의 DNS 레코드는 개인 끝점을 삭제 하거나 배치 계정에서 지역을 제거할 때 자동으로 제거 되지 않습니다. 이 개인 DNS 영역에 연결 된 새 개인 끝점을 추가 하기 전에 DNS 레코드를 수동으로 제거 해야 합니다. DNS 레코드를 정리 하지 않으면 개인 끝점 제거 또는 지역 제거 후에 추가 된 지역에 대 한 데이터 중단이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크에서 Batch 풀을 만드는](batch-virtual-network.md)방법에 대해 알아봅니다.
- [지정 된 공용 IP 주소를 사용 하 여 Batch 풀을 만드는](create-pool-public-ip.md)방법을 알아봅니다.
- [Azure 개인 링크](../private-link/private-link-overview.md)에 대해 알아봅니다.
