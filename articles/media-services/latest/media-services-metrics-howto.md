---
title: Azure Monitor의 메트릭 보기
description: 이 아티클에서 Azure portal 차트 및 Azure CLI를 사용 하 여 메트릭을 모니터링 하는 방법에 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795816"
---
# <a name="monitor-media-services-metrics"></a>Media Services 메트릭 모니터링 

[Azure Monitor](../../azure-monitor/overview.md) 응용 프로그램을 수행 하는 방법을 이해할 수 있도록 진단 로그 및 메트릭 모니터링 하는 사용 하도록 설정 합니다. 에 대 한 자세한 설명은 기능 및 Azure Media Services 메트릭 및 진단 로그를 사용 하는 이유를 보려면 [Media Services 모니터링 메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)합니다.

Azure Monitor는 포털에서 해당 차트, REST API를 통한 액세스 또는 쿼리 하기 등, 메트릭과 상호 작용 하는 여러 방법을 제공 CLI를 사용 하 여 합니다. 이 아티클에서 Azure portal 차트 및 Azure CLI를 사용 하 여 메트릭을 모니터링 하는 방법에 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Media Services 계정 만들기](create-account-cli-how-to.md)
- 검토 [Media Services 모니터링 메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Azure portal에서 메트릭 보기

1. [https://portal.azure.com](https://portal.azure.com )에서 Azure Portal에 로그인합니다.
1. Azure Media Services 계정으로 이동 및 선택 **메트릭을**합니다.
1. 클릭 합니다 **리소스** 상자 및 메트릭을 모니터링 하려는 리소스를 선택 합니다. 

    합니다 **리소스 선택** 창이를 사용할 수 있는 리소스 목록이 오른쪽에 나타납니다. 이 경우에 표시 

    * &lt;Media Services 계정 이름&gt;
    * &lt;Media Services 계정 이름&gt;/&lt;스트리밍 엔드포인트 이름&gt;
    * &lt;저장소 계정 이름&gt;

    선택 하 고 리소스 키를 눌러 **적용**합니다. 지원 되는 리소스와 메트릭의 하는 방법에 대 한 자세한 내용은 참조 하세요 [Media Services 모니터링 메트릭을](media-services-metrics-diagnostic-logs.md)합니다.
 
    ![metrics](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > 메트릭을 모니터링 하려는 리소스 사이 전환 하려면 클릭 합니다 **리소스** 다시 상자 하 고이 단계를 반복 합니다.
1. (선택 사항) 차트를 (맨 위에 있는 연필 아이콘을 눌러 이름 편집) 이름을 지정 합니다.
1. 확인 하려는 메트릭을 추가 합니다.

    ![metrics](media/media-services-metrics/metrics03.png)
1. 차트를 대시보드에 고정할 수 있습니다.

## <a name="view-metrics-with-azure-cli"></a>Azure CLI를 사용 하 여 메트릭 보기

CLI 사용 하 여 "송신" 메트릭을 가져오려면 다음 실행 `az monitor metrics` CLI 명령:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

다른 메트릭을 가져오려면 관심 메트릭 이름을 "송신"으로 대체.

## <a name="see-also"></a>참고자료

* [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md)
* [만들기, 보기 및 메트릭 경고 Azure Monitor를 사용 하 여 관리](../../azure-monitor/platform/alerts-metric.md)합니다.

## <a name="next-steps"></a>다음 단계

[진단 로그](media-services-diagnostic-logs-howto.md)
