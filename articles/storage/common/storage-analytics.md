---
title: Azure Storage 분석을 사용하여 로그 및 메트릭 데이터 수집 | Microsoft Docs
description: 스토리지 분석을 사용하면 모든 Storage 서비스에 대한 메트릭 데이터를 추적하고 Blob, 큐 및 Table Storage에 대한 로그를 수집할 수 있습니다.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483539"
---
# <a name="storage-analytics"></a>저장소 분석

Azure Storage 분석은 로깅을 수행하며 Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. 사용할 수 있습니다 합니다 [Azure portal](https://portal.azure.com)합니다. 자세한 내용은 참조 하세요 [Azure portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)합니다. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. 사용 된 [Blob 서비스 속성 설정](/rest/api/storageservices/set-blob-service-properties), [큐 서비스 속성 설정](/rest/api/storageservices/set-queue-service-properties)를 [테이블 서비스 속성 설정](/rest/api/storageservices/set-table-service-properties), 및 [파일서비스속성설정](/rest/api/storageservices/Get-File-Service-Properties)각 서비스에 대해 Storage Analytics를 사용 하도록 설정 하는 작업입니다.

집계된 데이터는 알려진 로깅용 Blob 및 알려진 메트릭용 테이블에 저장됩니다. Blob service 및 Table service API를 사용하면 이러한 Blob와 테이블에 액세스할 수 있습니다.

스토리지 분석에 저장되는 데이터의 양은 20TB로 제한됩니다. 이 제한은 총 Storage 계정 제한과 관계없이 적용됩니다. 스토리지 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

## <a name="billing-for-storage-analytics"></a>저장소 분석 청구

모든 메트릭 데이터는 저장소 계정 서비스를 통해 작성됩니다. 따라서 저장소 분석에서 수행하는 각 쓰기 작업에 대해 요금이 청구됩니다. 또한 메트릭 데이터에 사용되는 저장소에도 요금이 청구됩니다.

저장소 분석에서 수행하는 다음 작업에 대해 요금이 청구될 수 있습니다.

* 로깅용 Blob 작성 요청
* 메트릭용 테이블 엔터티 작성 요청

데이터 보존 정책을 구성한 경우에는 저장소 분석에서 이전 로깅 및 메트릭 데이터를 삭제할 때 삭제 트랜잭션에 대해 요금이 부과되지 않습니다. 그러나 클라이언트의 삭제 트랜잭션에는 요금이 청구됩니다. 보존 정책에 대한 자세한 내용은 [저장소 분석 데이터 보존 정책 설정](https://msdn.microsoft.com/library/azure/hh343263.aspx)을 참조하세요.

### <a name="understanding-billable-requests"></a>청구 가능한 요청 이해

계정의 저장소 서비스에 대한 모든 요청에는 요금이 청구될 수도 있고 청구되지 않을 수도 있습니다. Storage 분석에서는 요청 처리 방법을 나타내는 상태 메시지를 포함하여 서비스에 대한 개별 요청을 기록합니다. 마찬가지로 서비스 및 해당 서비스의 API 작업에 대한 메트릭도 저장됩니다. 여기에는 특정 상태 메시지의 개수와 비율이 포함됩니다. 이러한 기능을 함께 사용하면 요금이 청구될 수 있는 요청을 분석하고 애플리케이션을 개선할 수 있으며 서비스에 대한 요청의 문제를 진단할 수 있습니다. 청구에 대한 자세한 내용은 [Azure Storage 청구 이해 - 대역폭, 트랜잭션 및 용량](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)을 참조하세요.

저장소 분석 데이터를 확인할 때는 [저장소 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 토픽의 표를 통해 요금이 청구될 수 있는 요청을 확인할 수 있습니다. 그런 후에 실제 로그 및 메트릭 데이터를 상태 메시지와 비교하여 특정 요청에 대해 요금이 부과되었는지 파악할 수 있습니다. 또한 이전 항목의 표를 통해 저장소 서비스 또는 개별 API 작업의 사용 가능 여부도 조사할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)
* [스토리지 분석 메트릭](storage-analytics-metrics.md)
* [스토리지 분석 로깅](storage-analytics-logging.md)
