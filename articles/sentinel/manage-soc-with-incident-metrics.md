---
title: Azure 센티널에서 인시던트 메트릭을 사용 하 여 SOC를 더 잘 관리 | Microsoft Docs
description: Azure 센티널 인시던트 메트릭 화면 및 통합 문서의 정보를 사용 하 여 SOC (Security Operations Center)를 관리 하는 데 도움을 줍니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 9d8d0fc46a463bda31595988d807854ef146d333
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761726"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>인시던트 메트릭을 사용하여 SOC를 효율적으로 관리

> [!IMPORTANT]
> 인시던트 메트릭 기능은 현재 공개 미리 보기로 제공 됩니다.
> 이러한 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

SOC (보안 작업 센터) 관리자는 팀의 성능을 측정 하기 위해 전체 효율성 메트릭과 측정값이 필요 합니다. 심각도, MITRE 전술, 심사 평균 시간, 평균 해결 시간 등과 같은 다양 한 기준으로 시간에 따른 인시던트 작업을 확인 하는 것이 좋습니다. 이제 Azure 센티널은 Log Analytics 및 함께 제공 되는 **보안 작업 효율성** 통합 문서에서 새 **securityincident** 테이블과 스키마를 사용 하 여이 데이터를 사용할 수 있도록 합니다. 시간이 지남에 따라 팀의 성능을 시각화 하 고이 정보를 사용 하 여 효율성을 향상 시킬 수 있습니다. 인시던트 테이블에 대해 사용자 고유의 KQL 쿼리를 작성 하 고 사용 하 여 특정 감사 요구 사항 및 Kpi에 맞는 사용자 지정 통합 문서를 만들 수도 있습니다.

## <a name="use-the-security-incidents-table"></a>보안 인시던트 표 사용

**Securityincident** 테이블은 Azure 센티널에 기본 제공 됩니다. **Logs**에서 **securityinsights** 컬렉션의 다른 테이블과 함께 찾을 수 있습니다. Log Analytics의 다른 테이블과 마찬가지로 쿼리를 수행할 수 있습니다.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="보안 인시던트 표":::

인시던트를 만들거나 업데이트할 때마다 새 로그 항목이 테이블에 추가 됩니다. 이를 통해 인시던트에 대 한 변경 내용을 추적할 수 있으며 더 강력한 SOC 메트릭을 사용할 수 있지만, 인시던트에 대해 중복 항목을 제거 해야 할 수 있으므로 (실행 중인 정확한 쿼리에 따라)이 테이블에 대 한 쿼리를 생성할 때이를 염두에 두어야 합니다. 

예를 들어 인시던트 번호로 정렬 되었지만 인시던트의 가장 최근의 로그만 반환 하려는 모든 인시던트의 목록을 반환 하려는 경우 집계 함수를 사용 하 여 KQL [요약 연산자](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator) 를 사용 하면 됩니다 `arg_max()` [aggregation function](https://docs.microsoft.com/azure/data-explorer/kusto/query/arg-max-aggfunction).


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>추가 샘플 쿼리

평균 종료 시간:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

심사 평균 시간:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>보안 작업 효율성 통합 문서

**Securityincidents** 표를 보완 하기 위해 SOC 작업을 모니터링 하는 데 사용할 수 있는 기본 **보안 작업 효율성** 통합 문서 템플릿을 제공 했습니다. 통합 문서에는 다음과 같은 메트릭이 포함 되어 있습니다. 
- 시간이 지남에 따라 생성 되는 인시던트 
- 분류, 심각도, 소유자 및 상태를 닫아 만든 인시던트 
- 심사 시간 평균 
- 평균 종결 시간 
- 심각도, 소유자, 상태, 제품 및 시간별로 생성 된 인시던트 
- 백분위 수 심사 시간 
- 백분위 수 시간 종료 시간 
- 소유자 당 심사 시간 평균 
- 최근 활동 
- 최근 닫은 분류  

Azure 센티널 탐색 메뉴에서 **통합 문서** 를 선택 하 고 **템플릿** 탭을 선택 하 여이 새 통합 문서 템플릿을 찾을 수 있습니다. 갤러리에서 **보안 작업 효율성** 을 선택 하 고 **저장 된 통합 문서 보기** 및 **템플릿 보기** 단추 중 하나를 클릭 합니다.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="보안 인시던트 통합 문서 갤러리":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="보안 인시던트 통합 문서 완료":::

템플릿을 사용 하 여 특정 요구 사항에 맞게 사용자 고유의 사용자 지정 통합 문서를 만들 수 있습니다.

## <a name="securityincidents-schema"></a>SecurityIncidents 스키마

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>다음 단계

- Azure Sentinel을 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- [Azure Sentinel에 데이터를 등록](quickstart-onboard.md)하고 [데이터 및 잠재적 위협을 표시](quickstart-get-visibility.md)하는 방법에 대해 알아봅니다.
