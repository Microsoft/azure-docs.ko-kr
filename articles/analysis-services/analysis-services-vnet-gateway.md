---
title: VNet 데이터 원본에 대한 Azure Analysis Services 구성 | Microsoft Docs
description: VNet (Azure Virtual Network)에서 데이터 원본에 대한 게이트웨이를 사용 하도록 Azure Analysis Services 서버를 구성 하는 방법에 대해 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572272"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Azure VNet(Virtual Network)에서 데이터 원본에 게이트웨이 사용

이 문서에서는 데이터 원본이 [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)에 있는 경우 사용할 Azure Analysis Services **AlwaysUseGateway** 서버 속성에 대해 설명 합니다.

## <a name="server-access-to-vnet-data-sources"></a>VNet 데이터 원본에 대한 서버 액세스

VNet을 통해 데이터 원본에 액세스하는 경우 Azure Analysis Services 서버는 해당 데이터 원본이 사용자 환경의 온-프레미스에 있는 경우 해당 데이터 원본에 연결해야 합니다. **AlwaysUseGateway** 서버 속성을 구성 하 여 [온-프레미스 게이트웨이](analysis-services-gateway.md)를 통해 모든 데이터 원본에 액세스 하도록 서버를 지정할 수 있습니다. 

Azure SQL Database Managed Instance 데이터 소스는 개인 IP 주소를 사용 하 여 Azure VNet 내에서 실행 됩니다. 인스턴스에서 공용 엔드포인트을 사용 하도록 설정한 경우에는 게이트웨이가 필요 하지 않습니다. 공용 엔드포인트을 사용할 수 없는 경우 온-프레미스 데이터 게이트웨이가 필요 하 고 AlwaysUseGateway 속성을 true로 설정 해야 합니다.

> [!NOTE]
> 이 속성은 온 [-프레미스 데이터 게이트웨이](analysis-services-gateway.md) 를 설치 하 고 구성한 경우에만 적용 됩니다. 게이트웨이는 VNet에 있을 수 있습니다.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway 속성 구성

1. SSMS > 서버 > **속성** > **일반**에서 **고급 속성 표시(모두)** 를 선택합니다.
2. **ASPaaS\AlwaysUseGateway**에서 **true**를 선택합니다.

    ![항상 게이트웨이 속성 사용](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>참고 항목:
[온-프레미스 데이터 원본에 연결](analysis-services-gateway.md)   
[온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)   
[Azure VNET(Virtual Network)](../virtual-network/virtual-networks-overview.md)   

