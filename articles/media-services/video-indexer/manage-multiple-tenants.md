---
title: Video Indexer를 사용하여 여러 테넌트 관리 - Azure
description: 이 문서에서는 Video Indexer를 사용하여 여러 테넌트를 관리하기 위한 다양한 통합 옵션을 제안합니다.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/10/2019
ms.author: ikbarmen
ms.openlocfilehash: e98cf44db01de1f99836e247066ba01006eac97b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553661"
---
# <a name="manage-multiple-tenants"></a>여러 테넌트 관리

이 문서에서는 Video Indexer를 사용하여 여러 테넌트를 관리하기 위한 다양한 옵션을 설명합니다. 시나리오에 가장 적합한 방법을 선택합니다.

* 테넌트당 Video Indexer 계정 사용
* 모든 테넌트에 대해 단일 Video Indexer 계정 사용
* 테넌트당 Azure 구독 사용

## <a name="video-indexer-account-per-tenant"></a>테넌트당 Video Indexer 계정 사용

이 아키텍처를 사용하는 경우 각 테넌트에 대해 Video Indexer 계정이 생성됩니다. 테넌트는 영구적인 컴퓨팅 계층에 완전히 격리됩니다.  

![테넌트당 Video Indexer 계정 사용](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>고려 사항

* 고객이 스토리지 계정을 공유하지 않습니다(고객이 수동으로 구성하지 않는 경우).
* 고객이 컴퓨팅(예약 단위)을 공유하지 않으며 서로의 처리 작업 시간에 영향을 주지 않습니다.
* Video Indexer 계정을 삭제하여 시스템에서 테넌트를 쉽게 제거할 수 있습니다.
* 테넌트 간에 사용자 지정 모델을 공유할 수 없습니다.

    사용자 지정 모델을 공유해야 하는 비즈니스 요구 사항이 없는지 확인합니다.
* 테넌트당 여러 개의 Video Indexer(및 관련 Media Services) 계정을 사용하므로 관리 작업이 더 어렵습니다.

> [!TIP]
> [Video Indexer 개발자 포털](https://api-portal.videoindexer.ai/)에서 시스템의 관리 사용자를 만들고 권한 부여 API를 사용하여 테넌트에 관련된 [계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token)을 제공합니다.

## <a name="single-video-indexer-account-for-all-users"></a>모든 사용자에 대해 단일 Video Indexer 계정 사용

이 아키텍처를 사용하는 경우 고객이 테넌트 격리를 담당합니다. 모든 테넌트는 단일 Azure Media Services 계정에 단일 Video Indexer 계정을 사용해야 합니다. 콘텐츠를 업로드, 검색 또는 삭제하는 경우 고객은 해당 테넌트에 적절한 결과를 필터링해야 합니다.

![모든 사용자에 대해 단일 Video Indexer 계정 사용](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

이 옵션을 사용하면 테넌트별로 모델을 필터링하여 테넌트 간에 사용자 지정 모델(개인, 언어 및 브랜드)을 공유하거나 격리할 수 있습니다.

[비디오를 업로드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)할 때 테넌트당 다른 파티션 특성을 지정할 수 있습니다. 이렇게 하면 [검색 API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?)에서 격리가 가능합니다. 검색 API에서 파티션 특성을 지정하면 지정된 파티션의 결과만 표시됩니다. 

### <a name="considerations"></a>고려 사항

* 테넌트 간에 콘텐츠 및 사용자 지정 모델을 공유할 수 있습니다.
* 한 테넌트가 다른 테넌트의 성능에 영향을 줍니다.
* 고객이 Video Indexer를 토대로 복잡한 관리 계층을 빌드해야 합니다.

> [!TIP]
> [priority](upload-index-videos.md) 특성을 사용하여 테넌트 작업의 우선 순위를 지정할 수 있습니다.

## <a name="azure-subscription-per-tenant"></a>테넌트당 Azure 구독 사용 

이 아키텍처를 사용하는 경우 각 테넌트에 고유한 Azure 구독이 있습니다. 테넌트 구독에서 각 사용자에 대한 새 Video Indexer 계정을 만듭니다.

![테넌트당 Azure 구독 사용](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>고려 사항

* 청구 분리가 가능한 유일한 옵션입니다.
* 이 통합은 테넌트당 Video Indexer 계정을 사용하는 경우보다 관리 오버헤드가 더 많습니다. 청구가 요구 사항이 아닌 경우 이 문서에 설명된 다른 옵션 중 하나를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md)
