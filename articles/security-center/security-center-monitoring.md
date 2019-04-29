---
title: Azure Security Center를 사용하여 보안 태세 강화 | Microsoft Docs
description: 이 문서는 Azure Security Center에서 리소스를 모니터링하여 보안 태세를 강화하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703882"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Azure Security Center를 사용하여 보안 태세 강화
이 문서는 보안 태세를 강화하는 데 도움이 됩니다. Azure Security Center의 모니터링 기능을 사용하여 리소스를 최대한 강력하게 보호하고 정책 준수를 모니터링하세요.

## <a name="how-do-you-strengthen-your-security-posture"></a>어떤 방법으로 보안 태세를 강화하시나요?
상황에 대응할 수 있도록 이벤트가 발생할 때까지 이벤트를 감시하면서 기다리는 것을 모니터링이라고 생각하는 경우가 많습니다. 보안 태세 강화란 리소스를 감사하여 조직의 표준 또는 모범 사례를 충족하지 않는 시스템을 식별하는 사전 예방 전략을 의미합니다.

구독의 리소스에 대해 [보안 정책](tutorial-security-policy.md)을 사용하도록 설정하면 보안 센터에서 리소스의 보안을 분석하여 잠재적 취약성을 식별합니다. 네트워크 구성 정보는 즉시 이용할 수 있지만, 에이전트가 설치된 VM 및 컴퓨터 수에 따라 VM 및 컴퓨터의 구성에 대한 정보(예: 보안 업데이트 상태 및 운영 체제 구성)를 수집하여 사용할 수 있게 하는 데 한 시간 이상이 걸릴 수 있습니다. **권장 사항** 타일에서 문제와 함께 네트워크를 강화하고 위험을 수정하기 위한 방법이 포함된 전체 목록을 볼 수 있습니다.

리소스의 보안 상태 및 리소스 종류별 모든 문제를 볼 수 있습니다.

- 컴퓨터 리소스 및 앱의 상태를 모니터링하고 보안 향상을 위한 권장 사항을 얻으려면 [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)를 참조하세요.
- 가상 머신, 네트워크 보안 그룹 및 엔드포인트와 같은 네트워크 리소스를 모니터링하고 보안 향상을 위한 권장 사항을 받으려면 [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)를 참조하세요. 
- SQL Server 및 저장소 계정과 같은 데이터 및 저장소 리소스를 모니터링하고 보안 향상을 위한 권장 사항을 받으려면 [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](security-center-sql-service-recommendations.md)를 참조하세요. 
- MFA 및 계정 권한을 비롯한 ID 및 액세스 리소스를 모니터링하고 보안 향상을 위한 권장 사항을 받으려면 [Azure Security Center에서 ID 및 액세스 모니터링](security-center-identity-access.md)을 참조하세요. 
- 리소스에 대한 Just-In-Time 액세스를 모니터링하려면 [Just-In-Time을 사용하여 가상 머신 액세스 관리](security-center-just-in-time.md)를 참조하세요. 


권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.



![리소스 보안 상태 타일](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure Security Center에서 보안 설정을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/): Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
