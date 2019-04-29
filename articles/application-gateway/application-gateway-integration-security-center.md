---
title: Azure Security Center와의 Application Gateway 통합 | Microsoft Docs
description: 이 페이지에서는 Application Gateway가 Azure Security Center에 통합되는 방법에 대한 정보를 제공합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122304"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Application Gateway와 Azure Security Center 간의 통합 개요

Application Gateway와 Security Center에서 웹 애플리케이션 리소스를 보호하는 방법에 대해 알아봅니다. Application Gateway WAF(웹 애플리케이션 방화벽)는 [Security Center](../security-center/security-center-intro.md)와 통합되어 사용자 환경에서 보호되지 않는 웹 애플리케이션에 대한 위협을 방지, 검색 및 대응할 수 있는 완벽한 보기를 제공합니다.

## <a name="overview"></a>개요

Application Gateway WAF는 악용 및 취약성으로부터 웹 애플리케이션을 보호하는 Security Center의 권장 사항입니다. WAF에서 보호되지 않는 웹 지원 리소스는 Security Center에서 높은 심각도 수준의 권장 사항으로 표시됩니다. 웹 애플리케이션 방화벽에 대한 권장 사항은 **개요** 페이지의 **애플리케이션** 아래에 표시됩니다.

![Security Center와의 통합][1]

웹 애플리케이션 방화벽과 관련된 권장 사항을 클릭하면 권장 사항의 세부 정보를 보여 주는 새 페이지가 열립니다.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>기존 리소스에 웹 애플리케이션 방화벽 추가

**모든 서비스** > **보안 + ID** > **Security Center**로 차례로 이동하고, **Security Center - 개요**에서 **애플리케이션**을 클릭합니다. **Security Center - 애플리케이션**의 표에는 Security Center를 통해 구독에서 검색한 애플리케이션 목록이 있습니다.

![웹 애플리케이션][3]

중요한 문제가 있는 웹 애플리케이션을 클릭하면 **애플리케이션 보안 상태** 페이지가 표시됩니다. 아래 이미지에서는 웹 애플리케이션 방화벽에서 보호되지 않는 웹 애플리케이션을 보여 줍니다. 

![보호되지 않는 웹 리소스][2]

**권장 사항** 아래에서 **웹 애플리케이션 방화벽 추가**를 클릭하여 **웹 애플리케이션 방화벽 추가** 페이지를 엽니다.

기존 Application Gateway가 없거나 새 Application Gateway를 만들려면, **새 웹 애플리케이션 방화벽 만들기**에서 **새로 만들기**, **Microsoft - Application Gateway**를 차례로 클릭합니다. 이를 통해 애플리케이션 게이트웨이를 단계적으로 만듭니다. 이 시점에서 웹 애플리케이션을 보호되는 리소스로 추가하면 Security Center에서 이 리소스가 웹 애플리케이션 방화벽으로 보호되고 있는지를 추적합니다. 이 경우 백 엔드 풀 멤버로는 추가하지 않습니다.

기존 애플리케이션 게이트웨이가 있는 경우 **기존 솔루션 사용** 아래에서 선택할 수 있습니다.

![웹 애플리케이션 방화벽 추가 페이지][4]

Security Center를 통해 애플리케이션 게이트웨이에 웹 애플리케이션을 추가하더라도 해당 리소스가 백 엔드 풀 멤버로 추가되지 않습니다. 이는 애플리케이션 게이트웨이 리소스에서 직접 추가해야 합니다.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>기존 웹 애플리케이션 방화벽에 리소스 추가

**모든 서비스** > **보안 + ID** > **Security Center**로 차례로 이동하고, **Security Center - 개요**에서 **파트너 솔루션**을 클릭합니다. 기존 Security Center 인식 애플리케이션 게이트웨이가 **파트너 솔루션** 페이지에 표시됩니다.

![파트너 솔루션][7]

**앱 연결**을 클릭하여 **링크 애플리케이션 연결** 페이지를 엽니다. 여기서는 기존 애플리케이션을 선택할 수 있는 옵션이 제공됩니다. 보호할 애플리케이션을 선택하고 **확인**을 클릭합니다. 이 경우 웹 애플리케이션은 애플리케이션 게이트웨이의 백 엔드 풀에 추가되지 않습니다. 이렇게 하면 Security Center에서 추적할 수 있도록 리소스를 보호된 리소스로 설정합니다. 리소스를 백 엔드 풀 멤버로 추가하려면 애플리케이션 게이트웨이에서 수행해야 합니다. 즉 현재 페이지에서 **솔루션 콘솔**을 클릭하여 웹 애플리케이션을 백 엔드 풀에 추가할 수 있는 애플리케이션 게이트웨이 리소스로 가져오면 됩니다.

![파트너 솔루션 애플리케이션][6]

## <a name="finalize-configuration"></a>구성 완료

Security Center에서는 애플리케이션 게이트웨이에 추가한 애플리케이션을 보호된 리소스로 추적합니다.  이 리소스의 상태를 모니터링하고, 해당 리소스가 애플리케이션 게이트웨이에서 보호되는지 확인합니다. 다음 단계는 가상 머신의 개인 IP, 공용 IP 또는 NIC를 애플리케이션 게이트웨이의 백 엔드 풀에 추가하는 것입니다. **애플리케이션 보호 마무리**에 대한 추가 권장 사항은 이 작업을 완료할 때까지 표시되며, 리소스를 추가하면 표시되지 않습니다.

![웹 애플리케이션 방화벽 추가 페이지][5]

## <a name="security-alerts"></a>보안 경고

Security Center에서 **검색** > **보안 경고**로 차례로 이동합니다.  여기서 애플리케이션 게이트웨이에 대한 WAF 경고를 찾습니다. 경고는 WAF 규칙으로 분류됩니다.

![보안 경고][8]

규칙을 클릭하면 해당 WAF 규칙에 대한 경고 목록이 제공됩니다. 경고마다 찾기에 대한 자세한 추가 정보가 표시됩니다. 세부 정보에서는 애플리케이션 게이트웨이에 대한 링크를 제공합니다.
 
![경고 세부 정보][9]

## <a name="next-steps"></a>다음 단계

기존 애플리케이션 게이트웨이에서 웹 애플리케이션 방화벽을 사용하도록 설정하는 방법을 알아보려면 [웹 애플리케이션 방화벽이 있는 Azure Application Gateway 만들기 또는 업데이트](application-gateway-web-application-firewall-portal.md)를 참조하세요.

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png