---
title: Azure Media Services v2 계정 관리 | Microsoft Docs
description: Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. 이 문서에서는 Azure Media Services v2 계정을 관리 하는 방법을 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981937"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure Media Services v2 계정 관리

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정 이동 

Media Services 계정을 새 구독으로 이동 해야 하는 경우 먼저 Media Services 계정을 포함 하는 전체 리소스 그룹을 새 구독으로 이동 해야 합니다. Azure Storage 계정, Azure CDN 프로필 등의 연결 된 모든 리소스를 이동 해야 합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요. Azure의 모든 리소스와 마찬가지로 리소스 그룹 이동은 완료 하는 데 다소 시간이 걸릴 수 있습니다.

Media Services v2는 다중 테넌트 모델을 지원 하지 않습니다. Media Services 계정을 새 테넌트의 구독으로 이동 해야 하는 경우 새 테넌트에 새 Azure Active Directory (Azure AD) 응용 프로그램을 만듭니다. 그런 다음 계정을 새 테넌트의 구독으로 이동 합니다. 테넌트 이동이 완료 된 후 새 테넌트에서 Azure AD 응용 프로그램을 사용 하 여 v2 Api를 사용 하 여 Media Services 계정에 액세스할 수 있습니다. 

> [!IMPORTANT]
> Media Services v2 API에 액세스 하려면 [AZURE AD 인증](media-services-portal-get-started-with-aad.md) 정보를 다시 설정 해야 합니다.  
### <a name="considerations"></a>고려 사항

* 다른 구독으로 마이그레이션하기 전에 계정에 있는 모든 데이터의 백업을 만듭니다.
* 모든 스트리밍 엔드포인트과 라이브 스트리밍 리소스를 중지 해야 합니다. 사용자는 리소스 그룹 이동 기간 동안 콘텐츠에 액세스할 수 없습니다. 

> [!IMPORTANT]
> 이동이 성공적으로 완료 될 때까지 스트리밍 엔드포인트을 시작 하지 마세요.

### <a name="troubleshoot"></a>문제 해결 

리소스 그룹 이동 후에 Media Services 계정 또는 연결 된 Azure Storage 계정이 "연결 끊김" 상태가 되 면 저장소 계정 키를 회전 해 보세요. 저장소 계정 키를 회전 해도 Media Services 계정의 "연결 끊김" 상태가 해결 되지 않으면 Media Services 계정의 "지원 + 문제 해결" 메뉴에서 새로운 지원 요청을 파일에 추가 합니다.  
 
## <a name="next-steps"></a>다음 단계

[계정 만들기](media-services-portal-create-account.md)
