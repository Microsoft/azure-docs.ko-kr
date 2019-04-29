---
title: Azure Portal을 통해 스트리밍 엔드포인트 관리 | Microsoft 문서
description: 이 항목에서는 Azure 포털을 사용하여 스트리밍 엔드포인트를 관리하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 1775bbb2913f6b1a985ca7ec9e89bafed42fd0e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61129714"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Azure 포털을 통해 스트리밍 엔드포인트 관리 

이 문서에서는 Azure Portal을 사용하여 스트리밍 엔드포인트를 관리하는 방법을 보여 줍니다. 

>[!NOTE]
>[개요](media-services-streaming-endpoints-overview.md) 문서를 검토해야 합니다. 

스트리밍 엔드포인트의 크기를 조정하는 방법에 대한 자세한 내용은 [이 문서](media-services-portal-scale-streaming-endpoints.md)를 참조하세요.

## <a name="start-managing-streaming-endpoints"></a>스트리밍 엔드포인트 관리 시작 

계정의 스트리밍 엔드포인트 관리를 시작하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 블레이드에서 **스트리밍 엔드포인트**를 선택합니다.
   
    ![스트리밍 엔드포인트](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.

## <a name="adddelete-a-streaming-endpoint"></a>스트리밍 엔드포인트 추가/삭제

>[!NOTE]
>기본 스트리밍 엔드포인트는 삭제할 수 없습니다.

Azure 포털을 사용하여 스트리밍 엔드포인트를 추가/삭제하려면 다음을 수행합니다.

1. 스트리밍 엔드포인트를 추가하려면 페이지 위쪽의 **+ 엔드포인트** 를 클릭합니다. 

    다른 CDN 및 직접 액세스를 사용하려는 경우 여러 스트리밍 엔드포인트를 배치하는 것이 좋습니다.

2. 스트리밍 엔드포인트를 삭제하려면 **삭제** 단추를 누릅니다.      
3. 스트리밍 엔드포인트를 시작하려면 **시작** 단추를 클릭합니다.
   
    ![스트리밍 엔드포인트](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>스트리밍 엔드포인트 구성
스트리밍 엔드포인트를 사용하면 다음 속성을 구성할 수 있습니다.

* 액세스 제어
* 캐시 제어
* 교차 사이트 액세스 정책

이러한 속성에 대한 자세한 정보는 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)를 참조하세요.

>[!NOTE]
>CDN을 사용하는 경우 IP 액세스에 액세스할 수 없습니다. IP 액세스는 CDN이 없을 때만 적용됩니다.

다음을 수행하여 스트리밍 엔드포인트를 구성할 수 있습니다.

1. 구성하려는 스트리밍 엔드포인트를 선택합니다.
2. **설정**을 클릭합니다.

필드에 대한 간략한 설명은 다음과 같습니다.

![스트리밍 엔드포인트](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. 최대 캐시 정책: 이 스트리밍 엔드포인트를 통해 제공되는 자산의 캐시 수명 주기를 구성하는 데 사용됩니다. 값을 설정하지 않으면 기본값이 사용됩니다. Azure Storage에서 기본값을 직접 정의할 수도 있습니다. 스트리밍 엔드포인트에 대해 Azure CDN을 사용하도록 설정한 경우에는 캐시 정책 값을 600초보다 작게 설정하면 안 됩니다.  
2. 허용된 IP 주소: 게시된 스트리밍 엔드포인트에 연결할 수 있는 IP 주소를 지정하는 데 사용됩니다. IP 주소를 지정하지 않은 경우 모든 IP 주소에서 연결할 수 있습니다. IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.255.0)')로 지정할 수 있습니다.
3. Akamai 서명 헤더 인증에 대한 구성: Akamai 서버의 서명 헤더 인증 요청을 구성하는 방법을 지정하는 데 사용됩니다. 만료 시간은 UTC 단위입니다.

## <a name="scale-your-premium-streaming-endpoint"></a>프리미엄 스트리밍 엔드포인트 규모 조정

자세한 내용은 [이](media-services-portal-scale-streaming-endpoints.md) 문서를 참조하세요.

## <a id="enable_cdn"></a>Azure CDN 통합 사용

새 계정을 만들면 기본 스트리밍 엔드포인트 Azure CDN 통합이 기본적으로 설정됩니다.

나중에 CDN을 사용/사용 안 함으로 설정하려면 스트리밍 엔드포인트가 **중지됨** 상태여야 합니다. Azure CDN 통합이 사용하도록 설정되고 변경 내용이 모든 CDN POP에서 활성화되려면 최대 2시간이 걸릴 수 있습니다. 그러나 스트리밍 엔드포인트를 시작하고 스트리밍 엔드포인트에서 중단 없이 스트리밍할 수 있으며, 통합이 완료되면 CDN에서 스트림이 전송됩니다. 프로비저닝 기간 동안에는 스트리밍 엔드포인트가 **시작** 상태가 되고 성능 저하를 보일 수 있습니다.

CDN 통합은 중국 및 연방 정부 지역을 제외한 모든 Azure 데이터 센터에서 사용됩니다.

CDN 통합을 사용하는 경우 **액세스 제어**, 사용자 지정 호스트 이름 및 **Akamai 서명 인증** 구성은 비활성화됩니다.
 
> [!IMPORTANT]
> Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN**에서 표준 스트리밍 엔드포인트에 구현됩니다. 프리미엄 스트리밍 엔드포인트는 모든 **Azure CDN 가격 책정 및 공급자**를 사용하여 구성할 수 있습니다. Azure CDN 기능에 대한 자세한 내용은 [CDN 개요](../../cdn/cdn-overview.md)를 참조하세요.
 
### <a name="additional-considerations"></a>추가 고려 사항

* 스트리밍 엔드포인트에 CDN이 사용되면 클라이언트에서는 원점으로부터 직접 콘텐츠를 요청할 수 없습니다. CDN을 사용하거나 사용하지 않고 콘텐츠를 테스트하는 기능이 필요하면 CDN이 사용하도록 설정되지 않은 또 다른 스트리밍 엔드포인트를 만들 수 있습니다.
* 스트리밍 엔드포인트 호스트 이름은 CDN을 사용하도록 설정한 후에도 동일하게 유지됩니다. CDN을 사용하도록 설정한 후 미디어 서비스 워크플로에 변경 내용을 적용할 필요가 없습니다. 예를 들어 스트리밍 엔드포인트 호스트 이름이 strasbourg.streaming.mediaservices.windows.net이면 CDN을 사용하도록 설정한 후에 똑같은 호스트 이름이 사용됩니다.
* 새 스트리밍 엔드포인트의 경우 새 엔드포인트를 만들어서 CDN을 사용하도록 설정할 수 있습니다. 기본 스트리밍 엔드포인트의 경우 먼저 엔드포인트를 중지하고 CDN을 사용/사용 안 함으로 설정해야 합니다.
* 표준 스트리밍 엔드포인트는 Azure 클래식 포털에서 **Verizon 표준 CDN 공급자**를 통해서만 구성할 수 있습니다. 그러나 REST API를 사용하여 다른 Azure CDN 공급자를 사용하도록 설정할 수 있습니다.

## <a name="configure-cdn-profile"></a>CDN 프로필 구성

위쪽에서 **CDN 관리** 단추를 선택하여 CDN 프로필을 구성할 수 있습니다.

![스트리밍 엔드포인트](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

