---
title: Azure Resource Manager 템플릿 샘플 - Azure Front Door
description: 기본 Front Door를 만들고 Front Door 속도 제한을 구성하기 위한 템플릿을 포함하여 Azure Front Door용 Resource Manager 템플릿 샘플에 대해 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: duau
ms.openlocfilehash: 961214b3a815eb8ae9b0fcb283599b3474d4706e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399364"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Front Door에 대한 Azure Resource Manager 배포 모델 템플릿

다음 표에는 Azure Front Door에 대한 Azure Resource Manager 배포 모델 템플릿 링크가 포함되어 있습니다. 

| 템플릿 | Description |
| ---| ---|
| [기본 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| 단일 백 엔드를 사용하여 기본 Front Door 구성을 만듭니다. |
| [다중 백 엔드 및 백 엔드 풀과 URL 기반 라우팅을 사용하여 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| URL 경로를 기준으로 백 엔드 풀의 여러 백 엔드 및 백 엔드 풀 간에 부하 분산이 구성된 Front Door를 만듭니다. |
| [Front Door를 사용하여 사용자 지정 도메인 온보딩](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Front Door에 사용자 지정 도메인을 추가합니다. |
| [지역 필터링을 사용하여 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| 특정 국가/지역의 트래픽을 허용/차단하는 Front Door를 만듭니다. |
| [Front Door에서 백 엔드에 대한 상태 프로브 제어](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| 프로브 경로 및 프로브가 전송되는 간격을 업데이트하여 Front Door를 업데이트함으로써 상태 프로브 설정을 변경합니다. |
| [활성/대기 백 엔드 구성을 사용하여 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| 활성/대기 애플리케이션 토폴로지에 대한 우선 순위 기반 라우팅을 보여 주는 Front Door를 만듭니다. 이러한 Front Door는 기본적으로 기본(최고 우선 순위) 백 엔드가 사용 불가능해질 때까지 이 백 엔드로 모든 트래픽을 전송합니다. |
| [특정 경로에 대해 캐싱이 사용되도록 설정된 Front Door 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| 정의된 라우팅 구성에 대해 캐싱이 사용되도록 설정되어 워크로드에 대한 정적 자산이 캐싱되는 Front Door를 만듭니다. |
| [Front Door 호스트 이름에 대한 세션 선호도 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Front Door를 업데이트하여 프론트 엔드 호스트에 대한 세션 선호도를 사용하도록 설정합니다. 이 경우 동일한 사용자 세션의 후속 트래픽이 동일한 백 엔드로 전송됩니다. |
| [클라이언트 IP 허용 목록 또는 차단 목록에 대해 Front Door 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| 클라이언트 IP를 사용하여 사용자 지정 액세스 제어를 사용하여 트래픽 특정 클라이언트 ID를 제한하도록 Front Door를 구성합니다. |
| [특정 http 매개변수로 작업을 수행하도록 Front Door 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| http 매개 변수와 함께 액세스 제어에 대한 사용자 지정 규칙을 사용하여 수신 요청에서 http 매개 변수를 기준으로 특정 트래픽을 허용하거나 차단하도록 Front Door를 구성합니다. |
| [Front Door 속도 제한 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| 제공된 프론트 엔드 호스트에 대한 수신 트래픽의 속도를 제한하도록 Front Door를 구성합니다. |
| | |

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
