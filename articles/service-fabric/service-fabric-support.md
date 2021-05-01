---
title: Azure Service Fabric 지원 옵션 알아보기
description: 지원되는 Azure Service Fabric 클러스터 버전 및 파일 지원 티켓에 대한 링크
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92316493"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 지원 옵션

Service Fabric 클러스터 및 애플리케이션 워크로드 관리 요구 사항을 충족하기 위해 여러 지원 요청 옵션을 만들었습니다. 필요한 지원의 긴급도와 문제의 심각도에 따라 적합한 옵션을 선택할 수 있습니다.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>프로덕션 문제 보고 또는 Azure에 대한 유료 지원 요청

Azure에서 실행 중인 Service Fabric 클러스터와 관련된 문제를 보고하려면 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 또는 [Microsoft 지원 포털](https://support.microsoft.com/oas/default.aspx?prid=16146)에서 지원 티켓을 엽니다.

다음에 대해 자세히 알아봅니다.
 
- [Azure에 대한 Microsoft의 지원](https://azure.microsoft.com/support/plans/?b=16.44)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

> [!Note]
> 브론즈 안정성 계층에서 실행하는 클러스터 또는 단일 노드 클러스터에서는 워크로드만 테스트할 수 있습니다. 브론즈 안정성 또는 단일 노드 클러스터에서 실행되는 클러스터에 문제가 발생하는 경우 Microsoft 지원 팀에서 문제 완화를 지원하지만 근본 원인을 분석하지는 않습니다. 자세한 내용은 [클러스터의 안정성 특성](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)을 참조하세요.
>
> 프로덕션 준비 클러스터에 필요한 사항에 대한 자세한 내용은 [프로덕션 준비 검사 목록](./service-fabric-production-readiness-checklist.md)을 참조하세요.

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>프로덕션 문제 보고 또는 독립 실행형 Service Fabric 클러스터에 대한 유료 지원 요청

온-프레미스 또는 기타 클라우드에서 실행되는 Service Fabric 클러스터와 관련된 문제를 보고하려면 [Microsoft 지원 포털](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 전문 지원에 대한 티켓을 열면 됩니다.

다음에 대해 자세히 알아봅니다.

- [온-프레미스에 대한 Microsoft의 전문 지원](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric 문제 보고

Service Fabric 문제를 보고하기 위해 GitHub 리포지토리를 설정했습니다.  다음과 같은 포럼을 적극적으로 모니터링하고 있습니다.

### <a name="github-repo"></a>GitHub 리포지토리 

[Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues)에서 Azure Service Fabric 문제를 보고합니다. 이 리포지토리는 문제를 보고하고 추적하며 Azure Service Fabric과 관련된 사소한 기능 요청을 만들기 위한 것입니다. **라이브 사이트 문제를 보고할 때는 이 매체를 사용하지 마세요**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 및 MSDN 포럼

[StackOverflow에서 Service Fabric 태그][stackoverflow] 및 [MSDN의 Service Fabric 포럼][msdn-forum]은 플랫폼의 작동 방식 및 특정 작업을 수행하는 방법에 대한 일반적인 질문을 제공하는 데 가장 적합합니다.

### <a name="azure-feedback-forum"></a>Azure 피드백 포럼

[Service Fabric에 대한 Azure 피드백 포럼][uservoice-forum]은 중요한 제품 기능 아이디어를 제출하기에 가장 적합한 곳입니다. 가장 인기 있는 요청을 검토하며 이를 중장기 계획에 반영합니다. 커뮤니티 내에서 제안하신 요구가 충분히 지원될 수 있게 최선을 다하고 있습니다.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 미리 보기 버전 - 프로덕션 사용이 지원되지 않습니다.

경우에 따라 초기 피드백을 조사할 중요한 기능 변경 사항이 포함된 특별 미리 보기 릴리스가 제공됩니다. 프로덕션 워크로드를 제공하지 않는 격리된 테스트 환경에서만 미리 보기 버전을 사용해야 합니다. 프로덕션 클러스터는 항상 지원되는 안정적인 Service Fabric 버전으로 실행해야 합니다. 이러한 미리 보기 릴리스에는 유료 지원 옵션을 제공하지 않습니다.

미리 보기 버전은 항상 주 버전과 부 버전 번호가 255로 시작합니다. 예를 들어 Service Fabric 버전 255.255.5703.949가 표시되는 경우 이 버전은 미리 보기로 제공되며 테스트 클러스터에서만 사용하기 위한 것입니다. 이러한 미리 보기 릴리스는 포함된 기능에 관한 자세한 내용과 함께 [Service Fabric 팀 블로그](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)에서 발표됩니다. [Azure Service Fabric 문제 보고서](#report-azure-service-fabric-issues)에 나열된 옵션 중 하나를 사용하여 질문을 하거나 피드백을 제공하세요.

## <a name="next-steps"></a>다음 단계

[지원되는 Service Fabric 버전](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform