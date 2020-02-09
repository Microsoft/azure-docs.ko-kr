---
title: Azure 센티널에서 구하기 라이브 스트림를 사용 하 여 위협 감지 | Microsoft Docs
description: 이 문서에서는 Azure 센티널에서 사냥 라이브 스트림를 사용 하 여 데이터를 추적 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: cabailey
ms.openlocfilehash: 5b347848c9c6a58a70ab1093a6f9c70b62f3f769
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900448"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Azure 센티널에서 구하기 라이브 스트림를 사용 하 여 위협 감지

> [!IMPORTANT]
> Azure 센티널의 구하기 라이브 스트림은 현재 공개 미리 보기로 제공 되며 테넌트에 점진적으로 롤아웃 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


라이브 스트림를 사용 하 여 이벤트가 발생할 때 새로 만든 쿼리를 테스트 하 고, 일치 하는 항목이 발견 되 면 세션에서 알림을 가져오고, 필요한 경우 조사를 시작할 수 있는 대화형 세션을 만들 수 있습니다. Log Analytics 쿼리를 사용 하 여 라이브 스트림 세션을 신속 하 게 만들 수 있습니다.

- **이벤트가 발생할 때 새로 만든 쿼리 테스트**
    
    이벤트에 적극적으로 적용 되는 현재 규칙에 대 한 충돌 없이 쿼리를 테스트 하 고 조정할 수 있습니다. 이러한 새 쿼리가 예상 대로 작동 하는지 확인 한 후에는 경고에 대 한 세션을 상승 시키는 옵션을 선택 하 여 사용자 지정 경고 규칙으로 승격 하는 것이 쉽습니다.

- **위협이 발생 하면 알림 받기**
    
    위협 데이터 피드를 집계 된 로그 데이터와 비교 하 여 일치가 발생 하면 알림을 받을 수 있습니다. 위협 데이터 피드는 잠재적 또는 현재 위협과 관련 된 지속적인 데이터 스트림 이므로 알림이 조직에 잠재적 위협을 나타낼 수 있습니다. 사용자 지정 경고 규칙을 유지 하는 오버 헤드 없이 잠재적 문제에 대 한 알림을 받으려면 사용자 지정 경고 규칙 대신 라이브 스트림 세션을 만듭니다.

- **조사 시작**
    
    호스트 또는 사용자와 같은 자산을 포함 하는 활성 조사가 있는 경우 해당 자산에서 발생 하는 로그 데이터의 특정 (또는 임의) 활동을 볼 수 있습니다. 해당 작업이 발생 하면 알림이 표시 될 수 있습니다.


## <a name="create-a-livestream-session"></a>라이브 스트림 세션 만들기

기존 구하기 쿼리에서 라이브 스트림 세션을 만들거나 처음부터 세션을 만들 수 있습니다.

1. Azure Portal에서 **센티널** > **위협 관리** > **구하기**로 이동 합니다.

2. 구하기 쿼리에서 라이브 스트림 세션을 만들려면 다음을 수행 합니다.
    
    1. **쿼리** 탭에서 사용할 구하기 쿼리를 찾습니다.
    2. 쿼리를 마우스 오른쪽 단추로 클릭 하 고 **라이브 스트림에 추가를**선택 합니다. 다음은 그 예입니다.
    
    > [!div class="mx-imgBorder"]
    > ![Azure 센티널 구하기 쿼리](./media/livestream/livestream-from-query.png)에서 라이브 스트림 세션을 만듭니다.

3. 라이브 스트림 세션을 처음부터 만들려면 다음을 수행 합니다. 
    
    1. **라이브 스트림** 탭을 선택 합니다.
    2. **라이브 스트림로 이동을**선택 합니다.
    
4. **라이브 스트림** 창에서 다음을 수행 합니다.
    
    - 쿼리에서 라이브 스트림을 시작한 경우 쿼리를 검토 하 고 원하는 변경 작업을 수행 합니다.
    - 라이브 스트림를 처음부터 시작 했으면 쿼리를 만듭니다. 

5. 명령 모음에서 **재생** 을 선택 합니다.
    
    명령 모음 아래의 상태 표시줄에는 라이브 스트림 세션이 실행 중인지 아니면 일시 중지 되었는지 여부가 표시 됩니다. 다음 예제에서는 세션이 실행 됩니다.
    
    > [!div class="mx-imgBorder"]
    > ![Azure 센티널 구하기](./media/livestream/livestream-session.png)에서 라이브 스트림 세션을 만듭니다.

6. 명령 모음에서 **저장** 을 선택 합니다.
    
    **일시 중지**를 선택 하지 않는 한 세션은 Azure Portal에서 로그 아웃 될 때까지 계속 실행 됩니다.

## <a name="view-your-livestream-sessions"></a>라이브 스트림 세션 보기

1. Azure Portal에서 **센티널** > **위협 관리** > **구하기** > **라이브 스트림** 탭으로 이동 합니다.

2. 보거나 편집 하려는 라이브 스트림 세션을 선택 합니다. 다음은 그 예입니다.
    
    > [!div class="mx-imgBorder"]
    > ![Azure 센티널 구하기 쿼리](./media/livestream/livestream-tab.png)에서 라이브 스트림 세션을 만듭니다.
    
    선택한 라이브 스트림 세션이 재생, 일시 중지, 편집 등으로 열립니다.

## <a name="receive-notifications-when-new-events-occur"></a>새 이벤트가 발생할 때 알림 받기

새 이벤트에 대 한 라이브 스트림 알림은 Azure Portal 알림을 사용 하기 때문에 Azure Portal를 사용할 때마다 이러한 알림이 표시 됩니다. 다음은 그 예입니다.

![라이브 스트림에 대 한 Azure Portal 알림](./media/livestream/notification.png)

알림을 선택 하 여 **라이브 스트림** 창을 엽니다.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>라이브 스트림 세션을 경고로 상승

관련 라이브 스트림 세션의 명령 모음에서 **경고로 상승을** 선택 하 여 라이브 스트림 세션을 새 경고로 승격할 수 있습니다.

> [!div class="mx-imgBorder"]
> 라이브 스트림 세션을 경고로 상승 ![](./media/livestream/elevate-to-alert.png)

이 작업을 수행 하면 라이브 스트림 세션과 연결 된 쿼리로 미리 채워져 있는 규칙 만들기 마법사가 열립니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에서 사냥 라이브 스트림를 사용 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협에 대 한 사전 구하기](hunting.md)
- [노트북을 사용 하 여 자동화 된 구하기 캠페인 실행](notebooks.md)
