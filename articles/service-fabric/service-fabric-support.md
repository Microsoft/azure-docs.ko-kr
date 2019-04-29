---
title: Azure Service Fabric 지원 옵션 살펴보기 | Microsoft Docs
description: 지원되는 Azure Service Fabric 클러스터 버전 및 파일 지원 티켓에 대한 링크
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: a931de8be07d41cf4daab63aa7691973ee158452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545064"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 지원 옵션

애플리케이션 워크로드가 실행되는 Service Fabric 클러스터에 대한 적절한 지원을 제공하기 위해 다양한 옵션을 설정했습니다. 필요한 지원 수준 및 문제의 심각도에 따라, 올바른 옵션을 선택해야 합니다. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>프로덕션 문제 보고 또는 Azure에 대한 유료 지원 요청

Azure에 배포된 Service Fabric 클러스터의 문제를 보고하여 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 또는 [Microsoft 지원 포털](https://support.microsoft.com/oas/default.aspx?prid=16146)에서 지원에 대한 티켓을 엽니다.

다음에 대해 자세히 알아봅니다.
 
- [Azure에 대한 Microsoft의 지원](https://azure.microsoft.com/support/plans/?b=16.44)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

> [!Note]
> 브론즈 안정성 계층에서 실행 되는 클러스터를 사용 하면 테스트 워크 로드의 경우에 실행할 수 있습니다. 브론즈 안정성 실행 되는 클러스터를 사용 하 여 문제가 있는 경우 Microsoft 지원 팀에 문제를 완화 하는 데 도움이 됩니다 있지만 근본 원인 분석을 수행 하지 않습니다. 참조 하십시오 [클러스터의 안정성 특성](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) 대 한 자세한 내용은 합니다.
>
> 프로덕션 준비 클러스터에 필요한 것에 대 한 자세한 내용은 참조 하십시오 합니다 [프로덕션 준비 검사 목록](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)합니다.

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>프로덕션 문제 보고 또는 독립 실행형 Service Fabric 클러스터에 대한 유료 지원 요청

프리미어 또는 기타 클라우드에 배포된 Service Fabric 클러스터의 문제를 보고하려면 [Microsoft 지원 포털](https://support.microsoft.com/oas/default.aspx?prid=16146)에서 전문 지원에 대한 티켓을 여세요.

다음에 대해 자세히 알아봅니다.

- [온-프레미스에 대한 Microsoft의 전문 지원](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric 문제 보고

Service Fabric 문제를 보고하기 위해 GitHub 리포지토리를 설정했습니다.  다음과 같은 포럼을 적극적으로 모니터링하고 있습니다.

### <a name="github-repo"></a>GitHub 리포지토리 

[Service-Fabric-issues git repo](https://github.com/Azure/service-fabric-issues)에서 Azure Service Fabric 문제를 보고합니다. 이 리포지토리는 Azure Service Fabric의 문제를 보고 및 추적하고 사소한 기능 요청을 만드는 데 사용됩니다. **라이브 사이트 문제를 보고할 때는 이 리포지토리를 사용하지 마세요**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 및 MSDN 포럼

[StackOverflow에서 Service Fabric 태그][stackoverflow] 및 [MSDN의 Service Fabric 포럼][msdn-forum]은 플랫폼의 작동 방식 및 특정 작업을 수행하는 방법에 대한 질문을 제공하는 데 가장 적합합니다.

### <a name="azure-feedback-forum"></a>Azure 피드백 포럼

[Service Fabric을 위한 Azure 피드백 포럼][uservoice-forum]은 중장기적 계획의 일환으로 가장 인기 있는 요청을 검토하면서 제품에 대해 갖게 된 포괄적인 기능 아이디어를 제출할 수 있는 유용한 장소입니다. 커뮤니티 내에서 제안하신 요구가 충분히 지원될 수 있게 최선을 다하고 있습니다.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 미리 보기 버전 - 프로덕션 사용이 지원되지 않습니다.

종종 피드백이 필요한 중요한 기능이 포함된 버전이 미리 보기로 릴리스됩니다. 이러한 미리 보기 버전은 테스트 용도로만 사용해야 합니다. 프로덕션 클러스터는 항상 지원되는 안정적인 Service Fabric 버전으로 실행해야 합니다. 미리 보기 버전은 항상 주 버전과 부 버전 번호가 255로 시작합니다. 예를 들어 Service Fabric 버전 255.255.5703.949가 표시되면 해당 릴리스 버전은 테스트 클러스터에서만 미리 보기로 사용됩니다. 이러한 미리 보기 릴리스는 포함된 기능에 관한 자세한 내용과 함께 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric)에서 발표됩니다.
이러한 미리 보기 릴리스를 위한 무료 지원 옵션이 있습니다. [Azure Service Fabric 문제 보고서](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues)에 나열된 옵션 중 하나를 사용하여 질문을 하거나 피드백을 제공하세요.

## <a name="next-steps"></a>다음 단계

[지원되는 Service Fabric 버전](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples