---
title: Service Health 개요 | Microsoft Docs
description: Azure 앱이 현재 및 향후 Azure 서비스 문제 및 유지 관리에 의해 어떤 영향을 받는지에 대한 개인 설정된 정보입니다.
services: service-health
author: stephbaron
ms.author: stbaron
documentationcenter: service-health
ms.service: service-health
ms.topic: article
ms.workload: Supportability
ms.date: 03/27/2018
ms.openlocfilehash: 465e8751d02692648234a7a90b84b68f41522cb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620807"
---
# <a name="service-health"></a>서비스 상태
서비스 상태에서는 사용하는 영역에 있는 Azure 서비스의 상태를 추적하는 사용자 지정 가능한 대시보드를 제공합니다. 이 대시보드에서 지속적인 서비스 문제, 계획된 향후 유지 관리 또는 관련 상태 공지와 같은 활성 이벤트를 추적할 수 있습니다. 이벤트가 비활성화되면, 최대 90일 동안 상태 기록에 배치됩니다. 마지막으로 Service Health 대시보드를 사용하여 서비스 문제로 인해 영향이 발생할 경우 사전에 알리는 서비스 상태 경고를 만들고 관리할 수 있습니다.

## <a name="service-health-events"></a>Service Health 이벤트
Service Health는 리소스에 영향을 줄 수 있는 다음과 같은 세 가지 유형의 상태 이벤트를 추적합니다.
1. **서비스 문제** - 즉시 사용자에게 영향을 주는 Azure 서비스의 문제입니다. 
2. **계획된 유지 관리** - 나중에 서비스의 가용성에 영향을 줄 수 있는 예정된 유지 관리입니다.  
3. **상태 자문** - 주의가 필요한 Azure 서비스의 변경 내용입니다. Azure 기능이 사용되지 않거나 사용 할당량을 초과하는 경우를 예로 들 수 있습니다.

> [!NOTE]
> Service Health 이벤트를 보려면 사용자에게 구독의 reader 역할이 있어야 합니다.

## <a name="get-started-with-service-health"></a>Service Health 시작
Service Health 대시보드를 시작하려면 포털 대시보드에서 Service Health 타일을 선택합니다. 이전에 타일을 제거했거나 사용자 지정 대시보드를 사용 중인 경우 “추가 서비스”(대시보드의 왼쪽 아래)에서 Service Health 서비스를 검색합니다.

![Service Health 시작](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>서비스에 영향을 주는 현재 문제 확인
**서비스 문제** 보기에는 리소스에 영향을 주고 있는 Azure 서비스의 진행 중인 문제가 표시됩니다. 문제가 시작된 시점 및 영향을 받는 서비스 및 지역을 파악할 수 있습니다. 또한 최신 업데이트를 읽어 문제를 해결하기 위해 Azure에서 수행 중인 내용을 파악할 수 있습니다. 

![서비스 문제 관리](./media/service-health-overview/azure-service-health-overview-2.png)

**잠재적 영향** 탭을 선택하여 소유한 리소스 중에서 문제의 영향을 받을 수 있는 특정 리소스 목록을 확인할 수 있습니다. 이러한 리소스의 CSV 목록을 다운로드하여 팀과 공유할 수 있습니다.

![서비스 문제 관리 - 영향](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>링크 및 다운로드할 수 있는 설명 가져오기 
문제 관리 시스템에서 사용할 문제의 링크를 가져올 수 있습니다. PDF 및 경우에 따라 CSV 파일을 다운로드하여 Azure Portal에 액세스할 수 없는 사용자와 공유할 수 있습니다.   

![서비스 문제 관리 - 문제 관리](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Microsoft에서 지원 받기
문제가 해결된 후에도 리소스가 잘못된 상태로 남아 있는 경우 지원에 문의하세요.  페이지 오른쪽에 있는 지원 링크를 사용하세요.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>대시보드에 개인 설정된 상태 지도 고정
Service Health를 필터링하여 업무상 중요한 구독, 지역 및 리소스 종류를 표시합니다. 필터를 저장하고 개인 설정된 상태 세계 지도를 포털 대시보드에 고정합니다. 

![개인 설정된 상태 맵 필터링](./media/service-health-overview/azure-service-health-overview-6a.png)

![개인 설정된 상태 맵 핀 고정](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Service Health 경고 구성
Service Health는 Azure Monitor와 통합되어 업무상 중요한 리소스가 영향을 받는 경우 이일, 문자 메시지 및 웹후크 알림을 통해 경고합니다. 적절한 Service Health 이벤트에 대한 활동 로그 경고를 설정합니다. 작업 그룹을 사용하여 조직 내의 해당하는 사람들에게 해당 경고를 라우팅합니다. 자세한 내용은 [Service Health에 대한 경고 구성](../azure-monitor/platform/alerts-activity-log-service-notifications.md)을 참조하세요.

# <a name="next-steps"></a>다음 단계
상태 문제 알림을 받도록 경고를 설정합니다. 자세한 내용은 [Service Health에 대한 경고 구성](../azure-monitor/platform/alerts-activity-log-service-notifications.md)을 참조하세요. 
