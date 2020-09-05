---
title: Azure Stream Analytics 작업에 대 한 지리적 중복성 얻기
description: 이 문서에서는 지역 장애 조치 (failover) 대신 Azure Stream Analytics 작업의 지역 중복성을 구현 하는 방법을 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 44b0394773485f8054eddc94c7d7e9b91baebc6d
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951094"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업에 대 한 지리적 중복성 얻기

Azure Stream Analytics는 자동 지역 장애 조치 (failover)를 제공 하지 않지만, 여러 Azure 지역에 동일한 Stream Analytics 작업을 배포 하 여 지리적 중복성을 달성할 수 있습니다. 각 작업은 로컬 입력 및 로컬 출력 원본에 연결 됩니다. 응용 프로그램은 두 개의 지역 입력으로 입력 데이터를 보내고 두 개의 지역 출력 간에 조정 해야 합니다. Stream Analytics 작업은 별도의 두 엔터티입니다.

다음 다이어그램에서는 이벤트 허브 입력 및 Azure 데이터베이스 출력을 사용 하는 샘플 지역 중복 Stream Analytics 작업 배포를 보여 줍니다.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="지역 중복 stream analytics 작업 다이어그램":::

## <a name="primarysecondary-strategy"></a>기본/보조 전략

응용 프로그램은 주 데이터베이스로 간주 되는 지역 출력 데이터베이스를 관리 해야 하며 보조 데이터베이스로 간주 됩니다. 주 지역 장애 시 응용 프로그램은 보조 데이터베이스로 전환 되 고 해당 데이터베이스에서 업데이트를 읽기 시작 합니다. 중복 읽기를 최소화할 수 있는 실제 메커니즘은 응용 프로그램에 따라 달라 집니다.출력에 추가 정보를 기록 하 여이 프로세스를 간소화할 수 있습니다. 예를 들어 각 출력에 타임 스탬프 또는 시퀀스 ID를 추가 하 여 중복 행을 건너뛰는 작업을 수행할 수 있습니다. 주 지역이 복원 되 면 유사한 메커니즘을 사용 하 여 보조 데이터베이스를 사용 합니다.

서로 다른 지역에서 복제 옵션에 대해 다른 입력 및 출력 유형을 사용할 수 있지만이 문서에 설명 된 패턴을 사용 하 여 두 이벤트 생산자와 이벤트 소비자에 대 한 유연성과 제어를 제공 하기 때문에 지역 중복을 구현 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 Azure Stream Analytics 작업 모니터링 및 관리](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Azure Stream Analytics의 데이터 기반 디버깅](stream-analytics-job-diagram-with-metrics.md)