---
title: Azure Media Services v3 계정 관리 | Microsoft Docs
description: Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. 이 문서에서는 Azure Media Services v3 계정을 관리 하는 방법을 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 91e64ab524121faf6d4e363bfbfdea2d5db01941
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269661"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 계정 관리

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 자세한 내용은 [저장소 계정](storage-account-concept.md)을 참조 하세요.

## <a name="moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정 이동 

Media Services 계정을 새 구독으로 이동 해야 하는 경우 먼저 Media Services 계정을 포함 하는 전체 리소스 그룹을 새 구독으로 이동 해야 합니다. Azure Storage 계정, Azure CDN 프로필 등의 연결 된 모든 리소스를 이동 해야 합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요. Azure의 모든 리소스와 마찬가지로 리소스 그룹 이동은 완료 하는 데 다소 시간이 걸릴 수 있습니다.

> [!NOTE]
> Media Services v3은 다중 테 넌 트 모델을 지원 합니다.

### <a name="considerations"></a>고려 사항

* 다른 구독으로 마이그레이션하기 전에 계정에 있는 모든 데이터의 백업을 만듭니다.
* 모든 스트리밍 끝점과 라이브 스트리밍 리소스를 중지 해야 합니다. 사용자는 리소스 그룹 이동 기간 동안 콘텐츠에 액세스할 수 없습니다. 

> [!IMPORTANT]
> 이동이 성공적으로 완료 될 때까지 스트리밍 끝점을 시작 하지 마세요.

### <a name="troubleshoot"></a>문제 해결 

리소스 그룹 이동 후에 Media Services 계정 또는 연결 된 Azure Storage 계정이 "연결 끊김" 상태가 되 면 저장소 계정 키를 회전 해 보세요. 저장소 계정 키를 회전 해도 Media Services 계정의 "연결 끊김" 상태가 해결 되지 않으면 Media Services 계정의 "지원 + 문제 해결" 메뉴에서 새로운 지원 요청을 파일에 추가 합니다.  

## <a name="next-steps"></a>다음 단계

[계정 만들기](./create-account-howto.md)
