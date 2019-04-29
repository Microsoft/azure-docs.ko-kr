---
title: Azure Sentinel 미리 보기를 사용 하 여 경고를 조사할 | Microsoft Docs
description: 이 자습서를 사용 하 여 Azure Sentinel 경고를 조사 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: a0a16fbda662ee1d3718dbafc4231de67aab277e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614841"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>자습서: Azure Sentinel 미리 보기를 사용 하 여 위협 감지

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

한 후 [데이터 원본 연결](quickstart-onboard.md) Azure Sentinel 하려는 문제가 의심 스러운 경우 알림을 받을 합니다. 이 작업을 수행할 수 있도록, 만든 Azure Sentinel 있습니다 고급 할당할 수 있는 사례를 생성 하는 경고 규칙 및 비정상 및 위협 환경에 긴밀 하 게 조사를 사용 하 여 있습니다. 

이 자습서를 사용 하면 Azure Sentinel 하 여 위협을 검색할 수 있습니다.
> [!div class="checklist"]
> * 검색 규칙 만들기
> * 위협에 대응

## <a name="create-detection-rules"></a>검색 규칙 만들기

사례를 조사 하려면 먼저 검색 규칙을 만들 수 있어야 합니다. 

> [!NOTE]
> Azure Sentinel에서 생성 된 경고를 통해 사용할 수 [Microsoft Graph 보안](https://aka.ms/securitygraphdocs)합니다. 참조 된 [Microsoft Graph 보안 경고 설명서](https://aka.ms/graphsecurityreferencebetadocs) 자세한 정보와 통합 파트너입니다.

검색 규칙에 기반한 위협 및 비정상을 바로 파악 하려는 사용자 환경에서 의심 스러운 될 수 있는 유형의 표시, 조사 및 재구성을 보장 합니다. 

1. Azure Sentinel 아래 Azure portal에서 선택 **Analytics**합니다.

   ![분석](./media/tutorial-detect-threats/alert-rules.png)

2. 맨 위 메뉴 모음에서 클릭 **+ 추가**합니다.  

   ![경고 규칙 만들기](./media/tutorial-detect-threats/create-alert-rule.png)

3. 아래 **경고 규칙 만들기**설명 하는 이름을 입력 하 고 설정 합니다 **심각도** 필요에 따라 합니다. 

4. Log Analytics에서 쿼리를 만들어 다음에 붙여 넣습니다 합니다 **경고 규칙 집합** 필드입니다. Azure 활동에는 비정상적인 다양 한 리소스를 만들 때 경고 생성 하는 하는 샘플 쿼리는 다음과 같습니다.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

5. 에 **엔터티 매핑을** 섹션 아래에 있는 필드를 사용 합니다 **엔터티 형식** Azure Sentinel에서 인식 하는 엔터티 필드를 쿼리에 열을 매핑할 합니다. 각 필드에 대 한 적절 한 엔터티 필드에 Log Analytics에서 만든 쿼리의 관련 열을 매핑하십시오. 아래 관련 열 이름을 선택 합니다 **속성**합니다. 각 엔터티는 예를 들어 SID, GUID 등의 여러 필드를 포함합니다. 상위 수준 엔터티가 아니라 필드의 모든 엔터티를 매핑할 수 있습니다.

6. 경고 트리거 조건 정의 **경고 트리거**합니다. 이 경고를 트리거하는 조건을 정의 합니다. 

7. 설정 된 **빈도** 쿼리를 실행할-5 분 마다 자주 또는 하루에 한 번으로 자주 빈도 대 한 합니다. 

8. 설정 된 **기간** -에서 쿼리를 실행 하는 데이터 크기에 대 한 기간을 제어 하려면 예를 들어, 실행할 수 있습니다 매시간 60 분의 데이터에서.

9. 설정할 수도 있습니다는 **억제**합니다. 제거는 동일한 인시던트에 대해 트리거되지 중복 된 경고를 중지 하려고 할 때 유용 합니다. 이 방식에서 특정 기간 동안 트리거되는 경고를 중지할 수 있습니다. 이 동일한 인시던트에 대해 중복 경고를 방지 하 고 허용 된 시간 동안에 대 한 연속 되는 경고를 표시 하지 않을 수 있습니다. 예를 들어 경우는 **예약 경고** **빈도** 60 분으로 설정 됩니다 및 **예약 기간 경고** 두 시간으로 설정 되어 정의 된 쿼리 결과 초과 하 고 임계값이 경고를 트리거합니다을 두 번 경우 지난 60 분을 먼저 검색 되 면 이며 다시 때 데이터 샘플링 되 고 2 시간에서 처음 60 분입니다. 경고가 발생할 경우 경고 기간에 설정 된 시간 동안 제거 되도록 하는 것이 좋습니다. 예에서 하려는 60 분 표시 안 함 설정 경고는 가장 최근의 시간 동안 발생 한 이벤트를 트리거할만 수 있도록 합니다.

8. 사용자 쿼리를 붙여 넣은 후 합니다 **집합 경고 규칙** 필드를 즉시 확인할 수 있습니다에서 경고의 시뮬레이션 **논리 경고 시뮬레이션** 수는 데이터의 양을 이해를 얻을 수 있도록 사용자가 만든 경고에 대 한 특정 시간 간격에 대해 생성 됩니다. 에 대해 설정한 따라 달라 집니다 **빈도** 하 고 **임계값**합니다. 평균적으로 경고를 트리거할 수 너무 자주 표시 되 면 평균 기준선 위에 있도록 높을수록 결과의 수를 설정 하려고 합니다.

9. 클릭 **만들기** 경고 규칙을 초기화 합니다. 경고가 만들어지면 경고를 포함 하는 경우 생성 됩니다. 행으로 정의 된 검색 규칙을 볼 수는 **보안 분석** 탭 합니다. 또한 일치 레코드 수-각 규칙에 대해 트리거되는 경고를 볼 수 있습니다. 설정할 수 있습니다.이 목록에서 해제 또는 각 규칙을 삭제 합니다. 선택할 수 있습니다도 오른쪽-편집, 비활성화, 복제, 일치 하는 항목을 표시 또는 규칙을 삭제 하는 각 경고에 대 한 행의 끝에 줄임표 (...). 합니다 **Analytics** 페이지는 모든 활성 경고 규칙의 갤러리, 템플릿을 비롯 하 여 사용 하도록 설정 하면 및 만든 템플릿 기반 경고 규칙입니다.

1. 경고 규칙의 결과 볼 수 있습니다 합니다 **사례** 페이지에서 위치를 같이 관리할 수 있습니다. [사례를 조사](tutorial-investigate-cases.md), 위협을 해결 하 고 합니다.



## <a name="respond-to-threats"></a>위협에 대응

Azure Sentinel 플레이 북을 사용 하 여 위협에 대응 하기 위한 두 가지 주요 옵션을 제공 합니다. 경고에 대 한 응답에서을 플레이 북을 수동으로 실행할 수 있습니다 하거나 경고가 트리거될 때 자동으로 실행 되도록 플레이 북을 설정할 수 있습니다.

- 플레이 북을 구성한 경우 경고가 트리거될 때 자동으로 실행 되도록 플레이 북을 설정 합니다. 

- 수동으로 클릭 하 여 경고를 내부에서 플레이 북을 실행할 **플레이 북 보기** 을 선택한 다음 플레이 북 실행 합니다.




## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Sentinel를 사용 하 여 위협 감지 시작 하는 방법을 알아보았습니다. 

위협에 대 한 대응을 자동화 하는 방법을 알아보려면 [자동화 된 플레이 북을 사용 하 여 위협에 대응할 방법을](tutorial-respond-threats-playbook.md)합니다.
> [!div class="nextstepaction"]
> [위협에 대처](tutorial-respond-threats-playbook.md) 위협에 대 한 사용자 응답을 자동화할 수 있습니다.

