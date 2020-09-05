---
title: Azure Monitor를 사용 하 여 메트릭 보기
description: 이 문서에서는 Azure Portal 차트 및 Azure CLI를 사용 하 여 메트릭을 모니터링 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: dc532df51b4bbe2e7da1b47dfcc8d2e05e42f796
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267589"
---
# <a name="monitor-media-services-metrics"></a>Media Services 메트릭 모니터링

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) 를 사용 하면 응용 프로그램의 작동 방식을 이해 하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. 이 기능에 대 한 자세한 설명 및 Azure Media Services 메트릭과 진단 로그를 사용 하려는 이유를 보려면 [Media Services 메트릭 및 진단 로그 모니터링](media-services-metrics-diagnostic-logs.md)을 참조 하세요.

Azure Monitor는 포털에서 차트를 작성 하거나 REST API를 통해 액세스 하거나 Azure CLI를 사용 하 여 쿼리 하는 등 메트릭과 상호 작용 하는 여러 가지 방법을 제공 합니다. 이 문서에서는 Azure Portal 차트 및 Azure CLI를 사용 하 여 메트릭을 모니터링 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Media Services 계정 만들기](./create-account-howto.md)
- [모니터 Media Services 메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md) 검토

## <a name="view-metrics-in-azure-portal"></a>Azure Portal에서 메트릭 보기

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. Azure Media Services 계정으로 이동 하 여 **메트릭**을 선택 합니다.
1. **리소스** 상자를 클릭 하 고 메트릭을 모니터링 하려는 리소스를 선택 합니다.

    **리소스 선택** 창이 오른쪽에 표시 되 고 사용 가능한 리소스 목록이 표시 됩니다. 이 경우 다음을 참조 하세요.

    * &lt;Media Services 계정 이름&gt;
    * &lt;Media Services 계정 이름 &gt; / &lt; 스트리밍 끝점 이름&gt;
    * &lt;저장소 계정 이름&gt;

    리소스를 선택 하 고 **적용**을 누릅니다. 지원 되는 리소스 및 메트릭에 대 한 자세한 내용은 [Media Services 메트릭 모니터링](media-services-metrics-diagnostic-logs.md)을 참조 하세요.

    ![메트릭](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > 메트릭을 모니터링 하려는 리소스를 전환 하려면 **리소스** 상자를 다시 클릭 하 고이 단계를 반복 합니다.
1. (선택 사항) 차트의 이름을 지정 합니다 (맨 위에 있는 연필을 눌러 이름을 편집).
1. 보려는 메트릭을 추가 합니다.

    ![메트릭](media/media-services-metrics/metrics03.png)
1. 차트를 대시보드에 고정할 수 있습니다.

## <a name="view-metrics-with-azure-cli"></a>Azure CLI를 사용 하 여 메트릭 보기

Azure CLI를 사용 하 여 "송신" 메트릭을 가져오려면 다음 명령을 실행 합니다 `az monitor metrics` .

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

다른 메트릭을 가져오려면 관심 있는 메트릭 이름으로 "송신"을 대체 합니다.

## <a name="see-also"></a>참고 항목

* [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>다음 단계

[진단 로그](media-services-diagnostic-logs-howto.md)
