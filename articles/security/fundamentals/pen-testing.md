---
title: 침투 테스트 | Microsoft Docs
description: 이 문서에서는 침투 테스트 프로세스에 대 한 개요와 Azure 인프라에서 실행 중인 앱에 대해 펜 테스트를 수행 하는 방법을 제공 합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: dfacf124f8db0e5323c9abff56c4a78f85f6f014
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816136"
---
# <a name="penetration-testing"></a>침투 테스트

애플리케이션 테스트 및 배포에 Azure를 사용하면 좋은 점 중 하나는 신속하게 환경을 얻을 수 있다는 점입니다. 자신의 온-프레미스 하드웨어 요청, 구입, “래킹과 스태킹(racking and stacking)”에 대해 걱정할 필요가 없습니다.

환경을 신속 하 게 만드는 것이 좋지만 정상적인 보안 성실을 수행 해야 합니다. 수행 하려는 작업 중 하나는 Azure에 배포 하는 응용 프로그램을 침투 테스트 하는 것입니다.

Microsoft가 [Azure 환경에서의 침투 테스트](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 수행하는 것에 관해 어쩌면 이미 알고 계실 것입니다. 이러한 테스트를 통해 Azure의 향상 된 기능을 활용할 수 있습니다.

응용 프로그램에 대 한 침투 테스트를 수행 하지는 않지만, 사용자가 원하는 응용 프로그램에 대 한 테스트를 수행 해야 한다는 것을 이해 하 고 있습니다. 이는 응용 프로그램의 보안을 향상 시킬 때 전체 Azure 에코 시스템의 보안을 강화 하는 데 도움이 되기 때문입니다.

2017 년 6 월 15 일부 터 Microsoft는 더 이상 Azure 리소스에 대 한 침투 테스트를 수행 하기 위해 사전 승인이 필요 하지 않습니다. 이 프로세스는 다른 Microsoft 클라우드 서비스에 적용할 수 없고 Microsoft Azure에만 관련됩니다.

>[!IMPORTANT]
>보안 테스트 작업을 수행한다는 것을 Microsoft에 알리는 작업은 더 이상 필수가 아니지만 고객은 여전히 [Microsoft 클라우드 통합 보안 테스트 지침서](https://technet.microsoft.com/mt784683)를 준수해야 합니다.

여러분이 수행할 수 있는 표준 테스트는 다음과 같습니다.

* [OWASP(Open Web Application Security Project) 취약점 상위 10개](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [퍼지 테스트](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [포트 검색](https://en.wikipedia.org/wiki/Port_scanner)

사용자가 수행할 수 없는 일종의 펜 테스트는 [DoS (서비스 거부)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 공격입니다. 이 테스트에는 DoS 공격 자체를 시작 하거나 모든 유형의 DoS 공격을 확인, 시연 또는 시뮬레이션할 수 있는 관련 테스트를 수행 하는 작업이 포함 됩니다.

>[!Note]
>Microsoft는 BreakingPoint Cloud와 협력하여 시뮬레이션을 위해 DDoS Protection을 사용하도록 설정된 공용 IP 주소에 대한 트래픽을 생성할 수 있는 인터페이스를 구축했습니다. 중단점 클라우드 시뮬레이션에 대 한 자세한 내용은 [DDoS 검색 유효성 검사](../../virtual-network/manage-ddos-protection.md#validate-ddos-detection)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)에 대해 자세히 알아보세요.
