---
title: 다른 Azure 서비스와 Azure DNS 사용
description: 이 학습 경로에서 Azure DNS 사용 하 여 다른 Azure 서비스에 대한 이름을 확인 하는 방법을 시작 하세요.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937246"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>다른 Azure 서비스와 함께 Azure DNS가 작동하는 방법

Azure DNS는 호스팅된 DNS 관리 및 이름 확인 서비스입니다. Azure에 배포한 다른 애플리케이션 및 서비스에 대한 공용 DNS 이름을 만드는 데 사용할 수 있습니다. 사용자 지정 도메인에 Azure 서비스의 이름을 만드는 작업은 간단합니다. 서비스에 대한 올바른 유형의 레코드만 추가하면 됩니다.

* 동적으로 할당된 IP 주소의 경우 Azure가 서비스에 대해 만든 DNS 이름에 매핑하는 DNS CNAME 레코드를 만들 수 있습니다. DNS 표준은 영역 루트에 대해 CNAME 레코드를 사용하지 못하도록 합니다. 대신, 별칭 레코드를 사용할 수 있습니다. 자세한 내용은 [자습서: Azure 공용 IP 주소를 가리키도록 별칭 레코드 구성](tutorial-alias-pip.md)을 참조하세요.
* 정적으로 할당된 IP 주소의 경우 영역 루트의 *naked 도메인* 이름을 포함한 모든 이름을 사용하여 DNS A 레코드를 만들 수 있습니다.

다음 표에서는 다양한 Azure 서비스에 사용할 수 있도록 지원되는 레코드 형식을 설명합니다. 표에서 볼 수 있듯이, Azure DNS는 인터넷 연결 네트워크 리소스에 대해 DNS 레코드만 지원합니다. Azure DNS는 내부, 프라이빗 주소의 이름 확인에 사용할 수 없습니다.

| Azure 서비스 | Linux | Description |
| --- | --- | --- |
| Azure Application Gateway |[프런트 엔드 공용 IP](dns-custom-domain.md#public-ip-address) |DNS A 또는 CNAME 레코드를 만들 수 있습니다. |
| Azure Load Balancer |[프런트 엔드 공용 IP](dns-custom-domain.md#public-ip-address) |DNS A 또는 CNAME 레코드를 만들 수 있습니다. 부하 분산 장치는 동적으로 할당된 IPv6 공용 IP 주소를 가질 수 있습니다. IPv6 주소에 대한 CNAME 레코드를 만듭니다. |
| Azure Traffic Manager |공용 이름 |Traffic Manager 프로필에 할당된 trafficmanager.net 이름에 매핑되는 별칭 레코드를 만들 수 있습니다. 자세한 내용은 [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)을 참조하세요. |
| Azure Cloud Services |[공용 IP](dns-custom-domain.md#public-ip-address) |정적으로 할당된 IP 주소의 경우 DNS A 레코드를 만들 수 있습니다. 동적으로 할당된 IP 주소의 경우 *cloudapp.net* 이름에 매핑하는 CNAME 레코드를 만들어야 합니다.|
| Azure App Service | [외부 IP](dns-custom-domain.md#app-service-web-apps) |외부 IP 주소의 경우 DNS A 레코드를 만들 수 있습니다. 그렇지 않으면 azurewebsites.net 이름에 매핑하는 CNAME 레코드를 만들어야 합니다. 자세한 내용은 [Azure 앱에 사용자 지정 도메인 이름 매핑](../app-service/app-service-web-tutorial-custom-domain.md)을 참조하세요. |
| Azure Resource Manager VM |[공용 IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM은 공용 IP 주소를 가질 수 있습니다. 공용 IP 주소가 있는 VM은 부하 분산 장치 뒤에도 올 수 있습니다. 공용 주소에 대한 DNS A, CNAME 또는 별칭 레코드를 만들 수 있습니다. 이 사용자 지정 이름을 사용하여 부하 분산 장치의 VIP를 우회할 수 있습니다. |
| 클래식 VM |[공용 IP](dns-custom-domain.md#public-ip-address) |PowerShell 또는 CLI를 사용하여 만든 클래식 VM은 동적 또는 고정(예약된) 가상 주소로 구성할 수 있습니다. DNS CNAME 또는 A 레코드를 각각 만들 수 있습니다. |
