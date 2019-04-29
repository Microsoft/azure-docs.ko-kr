---
title: Azure API Management 인스턴스 업그레이드 및 크기 조정 | Microsoft Docs
description: 이 항목에서는 Azure API Management 인스턴스를 업그레이드하고 크기 조정하는 방법을 설명합니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: ac8babf3a00c73b942ae64ac4cca00c7be7cfcfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859553"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management 인스턴스 업그레이드 및 크기 조정  

고객은 단위를 추가하고 제거하여 APIM(Azure API Management) 인스턴스의 크기를 조정할 수 있습니다. **단위**는 전용 Azure 리소스로 구성되며 월당 API 호출 수로 표현되는 특정 로드 베어링 용량을 포함합니다. 이 숫자는 호출 제한이 아닌 대략적인 용량 계획에 허용할 최대 처리량 값을 나타냅니다. 실제 처리량과 대기 시간은 동시 연결 수 및 속도, 구성된 정책의 종류 및 수, 요청 및 응답 크기, 백 엔드 대기 시간 등의 요소에 따라 크게 달라집니다.

단위당 용량 및 가격은 단위가 존재하는 **계층**에 따라 달라집니다. 계층은 **개발자**, **기본**, **표준** 및 **프리미엄**의 네 가지 중에서 선택할 수 있습니다. 계층 내에서 서비스에 대한 용량을 늘려야 할 경우 단위를 추가해야 합니다. APIM 인스턴스에서 현재 선택된 계층에서 단위 추가가 허용되지 않는 경우 상위 수준 계층으로 업그레이드해야 합니다.

단위당 가격 및 사용 가능한 기능(예: 다중 지역 배포)은 APIM 인스턴스에 대해 선택한 계층에 따라 달라집니다. [가격 정보](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 문서에서는 단위당 가격 및 각 계층의 기능에 대해 설명합니다. 

>[!NOTE]
>[가격 정보](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 문서에서는 계층별로 대략적인 단위 용량 수를 보여 줍니다. 보다 정확한 수치를 얻기 위해 API에 대해 현실적인 시나리오를 살펴보아야 합니다. [Azure API Management 인스턴스의 용량](api-management-capacity.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 따르려면 다음이 필요합니다.

+ 활성 Azure 구독

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

+ [Azure API Management 인스턴스의 용량](api-management-capacity.md)에 대한 개념을 이해합니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>업그레이드 및 크기 조정  

계층은 **개발자**, **기본**, **표준** 및 **프리미엄**의 네 가지 중에서 선택할 수 있습니다. **개발자** 계층은 프로덕션 환경이 아닌 서비스를 평가하는 데 사용해야 합니다. **개발자** 계층에는 SLA가 없으며 이 계층의 크기를 조정(추가/제거)할 수 없습니다. 

**기본**, **표준**, **프리미엄**은 SLA를 포함하고 크기를 조정할 수 있는 프로덕션 계층입니다. 가장 저렴한 **기본** 계층은 SLA를 포함하며 2개 단위까지 확장할 수 있습니다. **표준** 계층은 4개 단위까지 확장할 수 있습니다. **프리미엄** 계층에는 원하는 수의 단위를 추가할 수 있습니다.

**프리미엄** 계층을 사용하면 원하는 여러 Azure 지역 간에 단일 Azure API Management 인스턴스를 배포할 수 있습니다. Azure API Management 서비스를 처음 만들 때 인스턴스는 하나의 단위만 포함하며 이 인스턴스는 단일 Azure 지역에 상주합니다. 초기 지역은 **주** 지역으로 지정됩니다. 추가 지역은 쉽게 추가할 수 있습니다. 지역을 추가할 때 할당할 단위 수를 지정합니다. 예를 들어 **주** 지역에 한 단위를 포함하고 다른 지역에 5개 단위를 포함할 수 있습니다. 각 지역에 있는 트래픽에 대한 단위 수를 조정할 수 있습니다. 자세한 내용은 [여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법](api-management-howto-deploy-multi-region.md)을 참조하세요.

어떤 계층에서든 업그레이드 및 다운그레이드가 가능합니다. 프리미엄 계층에서 표준 또는 기본으로 다운그레이드할 경우 업그레이드 또는 다운그레이드는 VNET 또는 다중 지역 배포와 같은 일부 기능을 제거할 수 있습니다.

>[!NOTE]
>업그레이드 또는 크기 조정 프로세스를 적용하는 데는 15~45분 정도 소요될 수 있습니다. 완료되면 알림이 표시됩니다.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Azure Portal을 사용하여 업그레이드 및 크기 조정

![Azure Portal에서 APIM 규모 조정](./media/upgrade-and-scale/portal-scale.png)

1. [Azure Portal](https://portal.azure.com/)에서 APIM 인스턴스로 이동합니다.
2. 메뉴에서 **규모 및 가격 책정**을 선택합니다.
3. 원하는 계층을 선택합니다.
4. 추가하고자 하는 **단위** 수를 지정합니다. 슬라이더를 사용하거나 단위 수를 입력합니다.  
    **프리미엄** 계층을 선택하는 경우 먼저 지역을 선택해야 합니다.
5. **저장**을 누릅니다.

## <a name="next-steps"></a>다음 단계

- [여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법](api-management-howto-deploy-multi-region.md)
- [Azure API Management 서비스 인스턴스 크기를 자동으로 조정하는 방법](api-management-howto-autoscale.md)
