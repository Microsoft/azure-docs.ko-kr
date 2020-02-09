---
title: Azure API Management 서비스를 여러 Azure 지역에 배포
titleSuffix: Azure API Management
description: 여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442660"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법

Azure API Management는 여러 지역 배포를 지원 하므로 API 게시자는 지원 되는 여러 Azure 지역에서 단일 Azure API Management 서비스를 배포할 수 있습니다. 다중 지역 기능을 사용 하면 지리적으로 분산 된 API 소비자가 받은 요청 대기 시간을 줄이고 한 지역이 오프 라인 상태가 되는 경우 서비스 가용성을 향상 시킬 수 있습니다.

새 Azure API Management 서비스에는 처음에 하나의 [장치][unit]만 단일 Azure 지역 (주 지역)에 포함 됩니다. 기본 또는 보조 지역에 추가 지역을 추가할 수 있습니다. API Management 게이트웨이 구성 요소는 선택한 모든 기본 및 보조 지역에 배포 됩니다. 들어오는 API 요청은 자동으로 가장 가까운 지역으로 전달 됩니다. 지역이 오프 라인 상태가 되 면 API 요청은 실패 한 영역 주위에 자동으로 가장 가까운 다음 게이트웨이로 라우팅됩니다.

> [!NOTE]
> API Management의 게이트웨이 구성 요소만 모든 지역에 배포 됩니다. 서비스 관리 구성 요소 및 개발자 포털은 주 지역 에서만 호스팅됩니다. 따라서 주 지역 가동 중단이 발생 하는 경우에는 기본 지역이 다시 온라인 상태가 될 때까지 개발자 포털에 액세스 하 고 구성을 변경 하는 기능 (예: Api 추가, 정책 적용)이 손상 됩니다. 주 지역은 오프 라인 상태 이지만 보조 지역은 사용 가능한 최신 구성을 사용 하 여 API 트래픽을 계속 제공 합니다.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"></a>새 지역에 API Management 서비스 인스턴스 배포

> [!NOTE]
> API Management 서비스 인스턴스를 아직 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][create an api management service instance]를 참조 하세요.

Azure Portal에서 API Management 서비스 인스턴스에 대한 **규모 및 가격 책정** 탭으로 이동합니다.

![크기 조정 탭][api-management-scale-service]

새 지역에 배포하려면 도구 모음에서 **+ 지역 추가**를 클릭합니다.

![지역 추가][api-management-add-region]

드롭다운 목록에서 위치를 선택하고 슬라이더를 사용하여 단위 수를 설정합니다.

![단위 지정][api-management-select-location-units]

**추가**를 클릭하여 위치 테이블에 선택 항목을 배치합니다.

모든 위치를 구성할 때까지 이 프로세스를 반복한 후 도구 모음에서 **저장**을 클릭하여 배포 프로세스를 시작합니다.

## <a name="remove-region"></a>위치에 API Management 서비스 인스턴스 삭제

Azure Portal에서 API Management 서비스 인스턴스에 대한 **규모 및 가격 책정** 탭으로 이동합니다.

![크기 조정 탭][api-management-scale-service]

제거할 위치에 대해 테이블의 오른쪽 끝에 있는 **...** 단추를 사용하여 상황에 맞는 메뉴를 엽니다. **삭제** 옵션을 선택합니다.

삭제를 확인하고 **저장**을 클릭하여 변경 내용을 적용합니다.

## <a name="route-backend"> </a>지역 백 엔드 서비스로 API 호출 라우팅

Azure API Management는 백 엔드 서비스 URL을 하나만 제공합니다. 다양한 지역에 Azure API Management 인스턴스가 있더라도 API 게이트웨이는 하나의 지역에만 배포된 동일한 백 엔드 서비스로 요청을 전달합니다. 이런 경우 성능 향상은 Azure API Management 내에서 요청에 특정한 지역에 캐시된 응답에서만 발생하고 전 세계의 백 엔드에 계속 연결하는 경우 대기 시간이 길어질 수 있습니다.

시스템의 지리적 분포를 최대한 활용하려면 Azure API Management 인스턴스와 동일한 지역에 백 엔드 서비스를 배포해야 합니다. 그런 다음, 정책과 `@(context.Deployment.Region)` 속성을 사용하여 백 엔드의 로컬 인스턴스로 트래픽을 라우팅하면 됩니다.

1. Azure API Management 인스턴스로 이동하여 왼쪽 메뉴에서 **API**를 클릭합니다.
2. 원하는 API를 선택합니다.
3. **인바운드 처리**의 화살표 드롭다운에서 **코드 편집기**를 클릭합니다.

    ![API 코드 편집기](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. 조건부 `choose` 정책과 결합된 `set-backend`를 사용하여 파일의 `<inbound> </inbound>` 섹션에 적절한 라우팅 정책을 생성합니다.

    예를 들어 아래 XML 파일은 미국 서부와 동아시아 지역에 해당합니다.

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)를 사용 하 여 백 엔드 서비스를 시작 하 고, API 호출을 Traffic Manager에 전달 하 고, 라우팅을 자동으로 확인 하도록 할 수도 있습니다.

## <a name="custom-routing"> </a>사용자 지정 라우팅을 사용 하 여 지역 게이트웨이 API Management

API Management는 [가장 짧은 대기 시간](../traffic-manager/traffic-manager-routing-methods.md#performance)을 기준으로 요청을 지역 _게이트웨이로_ 라우팅합니다. API Management에서이 설정을 재정의할 수는 없지만 사용자 지정 라우팅 규칙으로 자신의 Traffic Manager를 사용할 수 있습니다.

1. 사용자 고유의 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)를 만듭니다.
1. 사용자 지정 도메인을 사용 하는 경우 API Management 서비스 대신 [Traffic Manager와 함께 사용](../traffic-manager/traffic-manager-point-internet-domain.md) 합니다.
1. [Traffic Manager에서 API Management 국가별 엔드포인트을 구성](../traffic-manager/traffic-manager-manage-endpoints.md)합니다. 지역 엔드포인트은 `https://<service-name>-<region>-01.regional.azure-api.net`의 URL 패턴을 따릅니다 (예: `https://contoso-westus2-01.regional.azure-api.net`).
1. [Traffic Manager에서 API Management 지역별 상태 엔드포인트을 구성](../traffic-manager/traffic-manager-monitoring.md)합니다. 지역별 상태 엔드포인트은 `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`의 URL 패턴을 따릅니다 (예: `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`).
1. Traffic Manager의 [라우팅 메서드를](../traffic-manager/traffic-manager-routing-methods.md) 지정 합니다.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
