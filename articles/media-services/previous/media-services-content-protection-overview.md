---
title: Azure Media Services를 사용한 콘텐츠 보호 | Microsoft Docs
description: 이 기사는 Media Services 콘텐츠 보호에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8259b58c7f30b63084e970bd9aed99642a43226f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216172"
---
# <a name="content-protection-overview"></a>콘텐츠 보호 개요 

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

Azure Media Services를 사용하여 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

다음 이미지는 Media Services 콘텐츠 보호 워크플로를 보여 줍니다. 

![PlayReady로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

이 문서에서는 Media Services를 사용한 콘텐츠 보호를 이해하는 것과 관련된 개념 및 용어를 설명합니다. 문서는 또한 콘텐츠를 보호하는 방법을 설명하는 문서에 대한 링크를 제공합니다. 

## <a name="dynamic-encryption"></a>동적 암호화
 Media Services를 사용하여 AES 암호화되지 않은 키 또는 PlayReady, Widevine 또는 FairPlay 등과 같은 DRM 암호화로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 현재, HLS(HTTP 라이브 스트리밍) MPEG DASH 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 점진적 다운로드에 대한 암호화는 지원되지 않습니다. 각 암호화 메서드는 다음 스트리밍 프로토콜을 지원합니다.

- AES: MPEG-DASH, 부드러운 스트리밍 및 HLS
- PlayReady: MPEG-DASH, 부드러운 스트리밍 및 HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

자산을 암호화하려면 암호화 콘텐츠 키를 자산에 연결하고 해당 키에 대해 권한 부여 정책도 구성해야 합니다. 콘텐츠 키는 Microsoft Azure Media Services에 의해 지정되거나 자동으로 생성될 수 있습니다.

또한 자산의 배달 정책을 구성해야 합니다. 저장소에서 암호화된 자산을 스트리밍하려면 자산 배달 정책을 구성하여 배달 방법을 지정해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 암호화되지 않은 키 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 Media Services 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 암호화되지 않은 키 및 DRM
고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은 AES 암호화를 사용하면 콘텐츠 키가 암호화되지 않은 형식으로 클라이언트에 전송된다는 것입니다. 그 결과 콘텐츠를 암호화하는 데 사용되는 키는 일반 텍스트로 클라이언트에 대한 네트워크 추적에서 볼 수 있습니다. AES-128 암호화되지 않은 키 암호화는 뷰어가 신뢰할 만한 당사자(예: 직원이 볼 수 있도록 회사 내에 배포되는 회사 비디오 암호화)인 사용 사례에 적합합니다.

PlayReady, Widevine, 및 FairPlay 은 모두 AES-128 암호화되지 않은 키와 비교해서 더 높은 수준의 암호화를 제공합니다. 콘텐츠 키는 암호화된 형식으로 전송됩니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. DRM은 뷰어가 신뢰할 만한 당사자가 아니고 가장 높은 수준의 보안이 필요한 사용 사례에 권장됩니다.

## <a name="storage-encryption"></a>저장소 암호화
저장소 암호화를 사용하면 AES 256비트 암호화를 사용하여 암호화되지 않은 콘텐츠를 로컬로 암호화할 수 있습니다. 그런 다음, Azure Storage 스토리지에 업로드할 수 있습니다. 이 스토리지에서는 암호화된 상태로 저장됩니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.

저장소에서 암호화된 자산을 배달하려면 Media Services에서 콘텐츠 배달 방법을 알 수 있도록 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 암호 해독하고 지정된 배달 정책(예: AES, 일반 암호화 또는 암호화 없음)을 사용하여 콘텐츠를 스트리밍합니다.

## <a name="types-of-encryption"></a>암호화 유형
PlayReady 및 Widevine은 일반 암호화(AES CTR 모드)를 활용합니다. FairPlay는 AES CBC 모드 암호화를 활용합니다. AES-128 암호화되지 않은 키 암호화는 봉투 암호화를 활용합니다.

## <a name="licenses-and-keys-delivery-service"></a>라이선스 및 키 배달 서비스
Microsoft Azure Media Services는 DRM(PlayReady, Widevine, FairPlay) 라이선스 및 AES 키를 인증된 클라이언트에 키 배달 서비스를 제공합니다. [Azure Portal](media-services-portal-protect-content.md), REST API 또는 .NET용 Media Services SDK를 사용하여 라이선스 및 키에 대한 권한 부여 및 인증 정책을 구성할 수 있습니다.

## <a name="control-content-access"></a>콘텐츠 액세스 제어
콘텐츠 키 권한 부여 정책을 구성하여 콘텐츠에 액세스하는 사람을 제어할 수 있습니다. 콘텐츠 키 권한 부여 정책은 개방형 또는 토큰 제한을 지원합니다.

### <a name="open-authorization"></a>Open Authorization
개방형 권한 부여 정책을 사용하여 콘텐츠 키는 어떤 클라이언트(제한 없음)에도 전송됩니다.

### <a name="token-authorization"></a>토큰 권한 부여
토큰 제한 권한 부여 정책을 사용하여 콘텐츠 키는 키/라이선스 요청에서 유효한 JWT(JSON Web Token) 또는 SWT(단순 웹 토큰)를 표시하는 클라이언트에만 보내질 수 있습니다. 이 토큰은 STS(보안 토큰 서비스)에 의해 발급되어야 합니다. Azure Active Directory를 STS로 사용하거나 사용자 지정 STS를 배포할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키/라이선스에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 요청된 키/라이선스를 클라이언트에 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 보안 토큰 서비스입니다. 때로 범위라고도 하는 대상은 토큰의 의도 또는 토큰에서 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

## <a name="streaming-urls"></a>스트리밍 URL
자산이 하나 이상의 DRM으로 암호화되어 있는 경우 스트리밍 URL에서 암호화 태그를 사용합니다(형식='m3u8-aapl', 암호화='xxx').

고려 사항은 다음과 같습니다.

* 암호화 형식은 한 개만 지정할 수 있습니다.
* 하나의 암호화가 자산에 적용되었다면 암호화 형식을 URL에 지정할 필요는 없습니다.
* 암호화 형식은 대/소문자를 구분하지 않습니다.
* 다음과 같은 암호화 형식을 지정할 수 있습니다.
  * **cenc**: PlayReady 또는 Widevine인 경우(일반 암호화)
  * **cbcs-aapl**: FairPlay인 경우(AES CBC 암호화)
  * **cbc**: AES 봉투 암호화

## <a name="next-steps"></a>다음 단계
다음 문서는 콘텐츠 보호를 시작하기 위한 다음 단계를 설명합니다.

* [저장소 암호화로 보호](media-services-rest-storage-encryption.md)
* [AES 암호화로 보호](media-services-protect-with-aes128.md)
* [PlayReady 및/또는 Widevine으로 보호 ](media-services-protect-with-playready-widevine.md)
* [FairPlay로 보호](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>관련 링크

* [JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Active Directory와 Azure Media Services OWIN MVC 기반 앱을 Azure Active Directory와 통합하고 JWT 클레임을 기반으로 하는 콘텐츠 키 배달을 제한합니다](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
