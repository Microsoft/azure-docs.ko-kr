---
title: Azure Security Center에서 VM 에이전트 사용 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **VM 에이전트 사용**을 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 12934ad53050d16b89dd5b4175ca19a24d1ec4d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916296"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Azure Security Center에서 VM 에이전트 사용
[데이터 컬렉션을 사용하도록 설정](security-center-enable-data-collection.md)하려면 VM(가상 머신)에 VM 에이전트를 설치해야 합니다.  Azure Security Center에서 VM 에이전트가 필요한 VM을 확인하고 해당 VM에서 VM 에이전트를 사용하는 것이 좋습니다.

Azure Marketplace에서 배포된 VM에 VM 에이전트가 기본적으로 설치됩니다. [VM 에이전트 및 확장 - 2부](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) 문서에 VM 에이전트 설치 방법이 설명되어 있습니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항 블레이드**에서 **VM 에이전트 활성화**를 선택합니다.
   ![VM 에이전트 사용][1]
2. 이렇게 하면 **VM 에이전트가 없거나 응답하지 않습니다.** 블레이드가 열립니다. 이 블레이드에 VM 에이전트가 필요한 VM을 나열합니다. VM 에이전트를 설치하려면 블레이드의 지침을 따릅니다.
   ![VM 에이전트가 없습니다][2]

## <a name="see-also"></a>참고 항목
보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md)-- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md)-- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/)-- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
