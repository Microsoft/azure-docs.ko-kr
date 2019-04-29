---
title: Azure Monitor의 모니터링 솔루션 대상 지정 | Microsoft Docs
description: 모니터링 솔루션을 대상으로 지정하면 모니터링 솔루션을 특정 에이전트 세트로 한정할 수 있습니다.  이 문서에서는 범위 구성을 만들고 솔루션에 적용하는 방법을 설명합니다.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 4082847e1871fc03713471b0c043dddb80f91b0d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110347"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Azure Monitor의 모니터링 솔루션 대상 지정(미리 보기)
모니터링 솔루션을 구독에 추가하면 기본적으로 Log Analytics 작업 영역에 연결된 모든 Windows 및 Linux 에이전트에 의해 자동 배포됩니다.  특정 에이전트 집합으로 제한하여 비용을 관리하고 솔루션에 대해 수집되는 데이터 양을 제한할 수 있습니다.  이 문서에서는 솔루션에 범위를 적용할 수 있는 기능인 **솔루션 대상 지정**을 사용하는 방법을 설명합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>솔루션 대상 지정 방법
솔루션 대상 지정은 다음 섹션에 설명된 것처럼 3단계로 진행됩니다. 


### <a name="1-create-a-computer-group"></a>1. 컴퓨터 그룹 만들기
Azure Monitor에서 [컴퓨터 그룹](../platform/computer-groups.md)을 만들어 범위에 포함하려는 컴퓨터를 지정합니다.  컴퓨터 그룹은 로그 쿼리를 기준으로 하거나 Active Directory 또는 WSUS 그룹 등의 다른 원본에서 가져올 수 있습니다. [아래에 설명된](#solutions-and-agents-that-cant-be-targeted) 것처럼 Azure Monitor에 직접 연결된 컴퓨터만 범위에 포함됩니다.

작업 영역에서 컴퓨터 그룹을 만든 후에는 하나 이상의 솔루션에 적용될 수 있는 범위 구성에 포함합니다.
 
 
### <a name="2-create-a-scope-configuration"></a>2. 범위 구성 만들기
 **범위 구성**은 하나 이상의 컴퓨터 그룹을 포함하고 하나 이상의 솔루션에 적용될 수 있습니다. 
 
 다음 프로세스를 사용하여 범위 구성을 만듭니다.  

 1. Azure Portal에서 **Log Analytics 작업 영역**으로 이동한 후 작업 영역을 선택합니다.
 2. **작업 영역 데이터 원본** 아래에 있는 작업 영역에 대한 속성에서 **범위 구성**을 선택합니다.
 3. **추가**를 클릭하여 새 범위 구성을 만듭니다.
 4. 범위 구성의 **이름**을 입력합니다.
 5. **컴퓨터 그룹 선택**을 클릭합니다.
 6. 만든 컴퓨터 그룹과 필요에 따라 구성에 추가할 다른 그룹을 선택합니다.  **선택**을 클릭합니다.  
 6. **확인**을 클릭하여 범위 구성을 만듭니다. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. 솔루션에 범위 구성을 적용합니다.
범위를 구성했으면 하나 이상의 솔루션에 적용할 수 있습니다.  단일 범위 구성을 여러 솔루션에서 사용할 수 있지만, 각 솔루션은 범위 구성을 하나만 사용할 수 있습니다.

다음 프로세스를 사용하여 범위 구성을 적용합니다.  

 1. Azure Portal에서 **Log Analytics 작업 영역**으로 이동한 후 작업 영역을 선택합니다.
 2. 작업 영역에 대한 속성에서 **솔루션**을 선택합니다.
 3. 범위를 지정하려는 솔루션을 클릭합니다.
 4. **작업 영역 데이터 원본** 아래에 있는 솔루션에 대한 속성에서 **솔루션 범위 지정**을 선택합니다.  이 옵션을 사용할 수 없으면 [이 솔루션을 대상으로 지정할 수 없습니다](#solutions-and-agents-that-cant-be-targeted).
 5. **범위 구성 추가**를 클릭합니다.  이 솔루션에 구성이 이미 적용되어 있으면 이 옵션을 사용할 수 없습니다.  다른 구성을 추가하기 전에 기존 구성을 제거해야 합니다.
 6. 만든 범위 구성을 클릭합니다.
 7. 구성의 **상태**가 **성공**인지 확인합니다.  상태 오류가 나타나면 구성 오른쪽에 있는 줄임표를 클릭한 다음 **범위 구성 편집**을 선택하여 변경합니다.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>대상으로 지정할 수 없는 솔루션 및 에이전트
다음은 솔루션 대상 지정에 사용할 수 없는 에이전트 및 솔루션에 대한 조건입니다.

- 솔루션 대상 지정은 에이전트에 배포하는 솔루션에만 적용됩니다.
- 솔루션 대상 지정은 Microsoft에서 제공하는 솔루션에만 적용됩니다.  [사용자 또는 파트너가 직접 만든](solutions-creating.md) 솔루션에는 적용되지 않습니다.
- Azure Monitor에 직접 연결되는 에이전트만 필터링할 수 있습니다.  솔루션은 범위 구성에 포함되는지 여부에 관계없이 연결된 Operations Manager 관리 그룹의 일부인 모든 에이전트에 자동으로 배포됩니다.

### <a name="exceptions"></a>예외
명시된 조건에 맞더라도 다음 솔루션에는 솔루션 대상 지정을 사용할 수 없습니다.

- 에이전트 상태 평가

## <a name="next-steps"></a>다음 단계
- [작업 영역에 Azure Log Analytics 모니터링 솔루션 추가](solutions.md)에서 사용자 환경에 설치할 수 있는 솔루션을 비롯한 모니터링 솔루션에 대해 자세히 알아보세요.
- [Azure Monitor 로그 쿼리의 컴퓨터 그룹](../platform/computer-groups.md)에서 컴퓨터 그룹 생성에 대해 자세히 알아보세요.
