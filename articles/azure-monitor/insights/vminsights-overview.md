---
title: VM용 Azure Monitor란?
description: Azure Vm의 상태와 성능을 모니터링 하 고 응용 프로그램 구성 요소와 해당 종속성을 자동으로 검색 하 고 매핑하는 VM용 Azure Monitor의 개요입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 5c3cb13d0b2da9370f402083d82397679f2c9343
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022499"
---
# <a name="what-is-azure-monitor-for-vms"></a>VM용 Azure Monitor란?

VM용 Azure Monitor는 실행 중인 프로세스 및 다른 리소스에 대 한 종속성을 포함 하 여 가상 머신 및 가상 머신 확장 집합의 성능 및 상태를 모니터링 합니다. 이를 통해 성능 병목 현상 및 네트워크 문제를 식별 하 여 중요 한 응용 프로그램의 예측 가능한 성능 및 가용성을 제공할 수 있으며 문제가 다른 종속성과 관련 되어 있는지 여부를 파악할 수도 있습니다.

VM용 Azure Monitor는 다음과 같은 Windows 및 Linux 운영 체제를 지원 합니다.

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 머신
- 온-프레미스 가상 머신
- 다른 클라우드 환경에서 호스트 되는 가상 컴퓨터
  



VM용 Azure Monitor은 데이터를 Azure Monitor 로그에 저장 합니다 .이를 통해 강력한 집계 및 필터링을 제공 하 고 시간에 따른 데이터 추세를 분석할 수 있습니다. 가상 컴퓨터에서 직접 단일 VM의이 데이터를 보거나, Azure Monitor를 사용 하 여 여러 Vm의 집계 보기를 제공할 수 있습니다.

![Azure Portal의 가상 머신 인사이트 큐브 뷰](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>가격 책정
VM용 Azure Monitor에 대 한 직접적인 비용은 없지만 Log Analytics 작업 영역에서 작업에 대 한 요금이 청구 됩니다. [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라, Azure Monitor용 VM에 다음에 대한 요금이 청구됩니다.

- 데이터는 에이전트에서 수집 작업 영역에 저장 됩니다.
- 로그 및 상태 데이터를 기반으로 하는 경고 규칙입니다.
- 경고 규칙에서 전송 되는 알림입니다.

로그 크기는 성능 카운터의 문자열 길이에 따라 달라 지 며 VM에 할당 된 논리 디스크 및 네트워크 어댑터의 수에 따라 증가할 수 있습니다. 이미 서비스 맵를 사용 하 고 있는 경우 Azure Monitor 데이터 형식으로 전송 되는 추가 성능 데이터만 변경 됩니다 `InsightsMetrics` .


## <a name="configuring-azure-monitor-for-vms"></a>VM용 Azure Monitor 구성
VM용 Azure Monitor를 구성 하는 단계는 다음과 같습니다. 각 단계에 대 한 자세한 지침을 보려면 각 링크를 따르세요.

- [Log Analytics 작업 영역을 만듭니다.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights 솔루션을 작업 영역에 추가 합니다.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [모니터링할 가상 컴퓨터 및 가상 컴퓨터 확장 집합에 에이전트를 설치 합니다.](vminsights-enable-overview.md)



## <a name="next-steps"></a>다음 단계

- 가상 컴퓨터에 대 한 모니터링을 사용 하도록 설정 하는 요구 사항 및 방법에 대 한 [VM용 Azure Monitor 배포](vminsights-enable-overview.md) 를 참조 하세요.

