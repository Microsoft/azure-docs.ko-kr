---
title: Azure 유지 관리 일정(미리 보기) | Microsoft Docs
description: 고객은 유지 관리 예약 기능을 사용하여 Azure SQL Data Warehouse 서비스에서 새 기능, 업그레이드 및 패치를 출시하는 데 사용하는 필요한 예약된 유지 관리 이벤트를 계획할 수 있습니다.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680440"
---
# <a name="change-a-maintenance-schedule"></a>유지 관리 일정 변경 

## <a name="portal"></a>포털
유지 관리 일정은 언제든지 업데이트하거나 변경할 수 있습니다. 선택한 인스턴스가 활성 유지 관리 주기를 통해 진행되는 경우 설정이 저장됩니다. 식별된 다음 유지 관리 기간 중에 활성화됩니다. 활성 유지 관리 이벤트 중에 데이터 웨어하우스를 모니터링하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/service-health/resource-health-overview). 

유지 관리 일정을 사용하려면 7일 동안 두 유지 관리 기간을 선택해야 합니다. 각 유지 관리 기간은 3 ~ 8시간이 될 수 있습니다. 유지 관리는 유지 관리 기간 내에서 언제든지 발생할 수 있지만 사전 알림 없이 시간 범위 외에 발생하지는 않습니다. 서비스가 데이터 웨어하우스에 새 코드를 배포할 때는 연결이 잠깐 동안 끊깁니다. 

## <a name="identifying-the-primary-and-secondary-windows"></a>기본 및 보조 기간 확인

기본 및 보조 기간은 개별적인 날짜 범위여야 합니다. 예를 들어 기본 기간을 화요일 - 목요일로, 보조 기간을 토요일 - 일요일로 할 수 있습니다.

데이터 웨어하우스에 대한 유지 관리 일정을 변경하려면 다음 단계를 완료합니다.
1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  업데이트하려는 데이터 웨어하우스를 선택합니다. 개요 블레이드에서 페이지가 열립니다. 
3.  개요 블레이드에서 **유지 관리 일정(미리 보기) 요약** 링크를 선택하여 유지 관리 일정 설정에 대한 페이지를 엽니다. 또는 왼쪽 리소스 메뉴에서 **유지 관리 일정** 옵션을 선택합니다.  

    ![개요 블레이드 옵션](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. 페이지 위쪽의 옵션을 사용하여 기본 유지 관리 기간의 기본 설정 범위(일)를 확인합니다. 여기서 선택한 범위에 따라 기본 기간이 평일인지 주말인지가 결정됩니다. 기간을 선택하면 드롭다운 값이 업데이트됩니다. 미리 보기 기간에는 사용 가능한 **요일** 옵션의 전체 집합이 아직 지원되지 않는 지역도 있을 수 있습니다.

   ![유지 관리 설정 블레이드](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 드롭다운 목록 상자를 사용하여 기본 설정된 기본 및 보조 유지 관리 기간을 선택합니다.
   - **요일**: 선택한 기간에 유지 관리를 수행할 기본 설정 요일입니다.
   - **시작 시간**: 유지 관리 기간에 대한 기본 설정 시작 시간입니다.
   - **시간 범위**: 시간 범위의 기본 설정 지속 시간입니다.

   선택한 값에 따라 블레이드 맨 아래의 **일정 요약**이 업데이트됩니다. 
  
6. **저장**을 선택합니다. 새 일정이 현재 활성화되었음을 확인하는 메시지가 표시됩니다. 

   유지 관리 예약을 아직 지원하지 않는 지역에서 일정을 저장하는 경우에는 다음 메시지가 표시됩니다. 선택한 지역에서 기능이 사용 가능해지면 설정이 저장 및 활성화됩니다.    

   ![지역 가용성에 대한 메시지](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>다음 단계
- 로그 경고 규칙용 웹후크 작업에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
- Azure Service Health에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/service-health/service-health-overview).


