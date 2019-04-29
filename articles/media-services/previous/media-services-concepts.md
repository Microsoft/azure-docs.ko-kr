---
title: Azure Media Services 개념 | Microsoft 문서
description: 이 항목에서는 Azure Media Services 개념에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 3b7b821227478d8c6b5859f24c50407f9eb213fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235283"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services 개념 

이 항목에서는 가장 중요한 Media Services 개념에 대한 개요를 제공합니다.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>자산 및 저장소
### <a name="assets"></a>자산
[자산](https://docs.microsoft.com/rest/api/media/operations/asset) 에는 디지털 파일(비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일 포함)과 이러한 파일에 대한 메타데이터가 포함됩니다. 디지털 파일은 자산에 업로드한 후 Media Services 인코딩 및 스트리밍 워크플로에서 사용할 수 있습니다.

자산은 Azure Storage 계정의 BLOB 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 BLOB으로 저장됩니다. 페이지 blob은 Azure Media Services에서 지원되지 않습니다.

업로드하고 자산에 저장할 미디어 콘텐츠를 결정할 때는 다음 사항을 고려해야 합니다.

* 자산에는 미디어 콘텐츠의 고유한 단일 인스턴스만 포함되어야 합니다. 예를 들어 TV 에피소드, 영화 또는 광고의 단일 편집이 포함됩니다.
* 자산에는 시청각 파일의 변환이나 편집이 여러 개 포함되어서는 안 됩니다. 잘못된 자산 사용의 한 가지 예는 둘 이상의 TV 에피소드, 광고 또는 단일 프로덕션의 여러 카메라 각도를 자산 내에 저장하려고 하는 경우입니다. 시청각 파일의 여러 가지 변환 또는 편집을 자산에 저장하면 워크플로 뒷부분에서 인코딩 작업 제출, 자산 배달의 스트리밍 및 보안 설정에 문제가 발생할 수 있습니다.  

### <a name="asset-file"></a>자산 파일
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) 은 Blob 컨테이너에 저장되는 실제 비디오 또는 오디오 파일을 나타냅니다. 자산 파일은 항상 자산과 연결되며 자산에는 하나 이상의 파일이 포함될 수 있습니다. 자산 파일 개체가 blob 컨테이너의 디지털 파일과 연결되지 않은 경우 Media Services 인코더 작업을 하지 못합니다.

**AssetFile** 인스턴스 및 실제 미디어 파일은 별개의 두 개체입니다. AssetFile 인스턴스는 미디어 파일에 대한 메타데이터를 포함하는 반면 미디어 파일은 실제 미디어 콘텐츠를 포함합니다.

Media Service API를 사용하지 않고 Media Services에서 생성된 Blob 컨테이너의 콘텐츠를 변경하려고 하면 안 됩니다.

### <a name="asset-encryption-options"></a>자산 암호화 옵션
업로드, 저장 및 배달하려는 콘텐츠의 유형에 따라 Media Services는 선택할 수 있는 다양한 암호화 옵션을 제공합니다.

>[!NOTE]
>암호화가 사용되지 않습니다. 기본값입니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다.

점진적 다운로드를 사용하여 MP4를 배달하려는 경우 이 옵션을 사용하여 콘텐츠를 업로드합니다.

**StorageEncrypted** – AES 256비트 암호화를 사용하여 암호화되지 않은 콘텐츠를 로컬에서 암호화한 다음에 암호화되어 저장된 Azure Storage에 업로드하려면 이 옵션을 사용합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다. 

저장소에서 암호화된 자산을 배달하려면 Media Services에서 콘텐츠 배달 방법을 알 수 있도록 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거하고 지정된 배달 정책(예: AES, PlayReady 또는 암호화 없음)을 사용하여 콘텐츠를 스트리밍합니다. 

**CommonEncryptionProtected** - 일반적인 암호화 또는 PlayReady DRM(예: PlayReady DRM으로 보호되는 부드러운 스트리밍)으로 콘텐츠를 암호화하거나 이미 암호화된 경우 콘텐츠를 업로드하려면 이 옵션을 사용합니다.

**EnvelopeEncryptionProtected** – AES(Advanced Encryption Standard)로 암호화된 HLS(HTTP 라이브 스트리밍)를 보호하거나 이미 보호된 경우 업로드하려면 이 옵션을 사용합니다. AES로 이미 암호화된 HLS를 업로드하려는 경우 Transform Manager로 암호화되어 있어야 합니다.

### <a name="access-policy"></a>액세스 정책
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) 는 자산에 대한 액세스 권한(예: 읽기, 쓰기 및 목록) 및 기간을 정의합니다. 일반적으로 자산에 포함된 파일에 액세스하는 데 사용되는 로케이터로 AccessPolicy 개체를 전달합니다.

>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 배치되는 로케이터에 대한 정책) 동일한 정책 ID를 사용해야 합니다. 자세한 내용은 [이 항목](media-services-dotnet-manage-entities.md#limit-access-policies)을 참조하세요.

### <a name="blob-container"></a>Blob 컨테이너
Blob 컨테이너는 Blob 집합의 그룹화를 제공합니다. Blob 컨테이너는 Media Services에서 액세스 제어의 경계 지점 및 자산에 대한 SAS(공유 액세스 서명)으로 사용됩니다. 한 Azure Storage 계정에 포함될 수 있는 Blob 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.

>[!NOTE]
> Media Service API를 사용하지 않고 Media Services에서 생성된 Blob 컨테이너의 콘텐츠를 변경하려고 하면 안 됩니다.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>로케이터
[로케이터](https://docs.microsoft.com/rest/api/media/operations/locator)는 자산에 포함된 파일에 액세스할 수 있는 진입점을 제공합니다. 액세스 정책은 클라이언트가 지정된 자산에 액세스할 수 있는 권한 및 기간을 정의하는 데 사용됩니다. 로케이터는 액세스 정책과 다 대 일 관계가 있으므로 로케이터에 따라 여러 클라이언트에 다양한 시작 시간 및 연결 유형을 제공할 수 있지만 모두 동일한 권한 및 기간을 사용합니다. 그러나 Azure 저장소 서비스에 의해 설정된 공유 액세스 정책 제한으로 인해 한 번에 5개 이상의 고유한 로케이터를 지정된 자산에 연결할 수 없습니다. 

Media Services는 두 가지 유형의 로케이터를 지원합니다. 미디어를 스트리밍(예: MPEG DASH, HLS 또는 부드러운 스트리밍)하거나 미디어 및 SAS URL 로케이터를 점진적으로 다운로드하는 데 사용되는 OnDemandOrigin 로케이터는 Azure Storage에서 미디어 파일을 업로드하거나 다운로드하는 데 사용됩니다. 

>[!NOTE]
>OnDemandOrigin 로케이터를 만들 때는 목록 권한(AccessPermissions.List)을 사용할 수 없습니다. 

### <a name="storage-account"></a>Storage 계정
Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 미디어 서비스 계정은 하나 이상의 저장소 계정에 연결될 수 있습니다. 전체 크기가 저장소 계정당 500TB를 초과하지 않을 경우 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다.  Media Services는 SDK 수준 도구를 제공하여 여러 저장소 계정을 관리하고 메트릭 또는 임의 분산에 따라 이러한 계정에 업로드하는 동안 자산 분산의 부하를 분산할 수 있습니다. 자세한 내용은 [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx)작업을 참조하세요. 

## <a name="jobs-and-tasks"></a>작업 및 태스크
[작업](https://docs.microsoft.com/rest/api/media/operations/job) 은 일반적으로 하나의 오디오/비디오 프레젠테이션을 처리(예: 인덱싱 또는 인코딩)하는 데 사용됩니다. 여러 비디오를 처리하려면 인코딩할 각 비디오에 대해 하나의 작업을 만듭니다.

작업에는 수행할 처리에 대한 메타데이터가 포함됩니다. 각 작업에는 원자성 처리 태스크, 해당 입력 자산, 출력 자산, 미디어 프로세서 및 관련 설정을 지정하는 하나 이상의 [태스크](https://docs.microsoft.com/rest/api/media/operations/task)가 포함됩니다. 작업 내의 태스크는 함께 연결할 수 있으며, 이때 한 태스크의 출력 자산은 다음 태스크의 입력 자산으로 제공됩니다. 이러한 방식으로 한 작업에는 미디어 프레젠테이션에 필요한 모든 처리가 포함될 수 있습니다.

## <a id="encoding"></a>Encoding
Azure Media Services는 클라우드에서 미디어의 인코딩에 대한 여러 옵션을 제공합니다.

Media Services로 시작하는 경우 코덱과 파일 형식 간의 차이점을 이해하는 것은 중요합니다.
코덱은 압축/압축 해제 알고리즘을 구현하는 소프트웨어이고 파일 형식은 압축된 비디오를 보관하는 컨테이너입니다.

Media Services는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 Media Services에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

[동적 패키징](media-services-dynamic-packaging-overview.md)을 활용하려면 mezzanine(원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩해야 하며 하나 이상의 표준 또는 프리미엄 스트리밍 엔드포인트가 시작된 상태여야 합니다.

Media Services는 이 문서에서 설명하는 다음 주문형 인코더를 지원합니다.

* [미디어 인코더 표준](media-services-encode-asset.md#media-encoder-standard)
* [미디어 인코더 Premium 워크플로](media-services-encode-asset.md#media-encoder-premium-workflow)

지원되는 인코더에 대한 자세한 내용은 [인코더](media-services-encode-asset.md)를 참조하세요

## <a name="live-streaming"></a>라이브 스트리밍
Azure Media Services에서 채널은 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 채널은 다음 두 가지 방법 중 하나로 라이브 입력 스트림을 받습니다.

* 온-프레미스 라이브 인코더가 다중 비트 전송률 RTMP 또는 부드러운 스트리밍(조각화된 MP4)을 채널에 보냅니다. 다중 비트 전송률 부드러운 스트리밍을 출력하는 라이브 인코더인 MediaExcel, Ateme, Imagine Communications, Envivio, Cisco 및 Elemental을 사용할 수 있습니다. RTMP를 출력하는 라이브 인코더는 Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision 및 Tricaster 인코더입니다. 어떠한 추가적인 트랜스코딩 및 인코딩 없이 채널을 통해 수집된 스트림이 통과합니다. 요청된 경우 Media Services는 고객에게 스트림을 배달합니다.
* 단일 비트 전송률 스트림(다음 형식 중 하나: RTMP 또는 부드러운 스트리밍(조각화된 MP4))은 Media Services를 사용하여 라이브 인코딩을 수행할 수 있는 채널에 전송됩니다. 그러면 채널은 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다. 요청된 경우 Media Services는 고객에게 스트림을 배달합니다.

### <a name="channel"></a>채널
Media Services에서, [채널](https://docs.microsoft.com/rest/api/media/operations/channel)은 라이브 스트리밍 콘텐츠 처리를 담당합니다. 채널은 라이브 트랜스코더에 제공될 입력 엔드포인트(수집 URL)를 제공합니다. 채널은 라이브 트랜스코더에서 라이브 입력 스트림을 수신하여 하나 이상의 StreamingEndpoints를 통해 스트리밍하는 데 사용할 수 있도록 합니다. 또한 채널은 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)를 제공합니다.

채널을 만들 때 수집 URL 및 미리 보기 URL을 얻을 수 있습니다. 이러한 URL을 얻으려면 채널이 시작됨 상태로 있을 필요가 없습니다. 라이브 트랜스코더에서 채널로 데이터를 푸시할 준비가 되면 채널을 시작해야 합니다. 라이브 트랜스코더에서 데이터 수집을 시작하면 스트림을 미리 볼 수 있습니다.

각 Media Services 계정에는 여러 채널, 여러 프로그램 및 여러 StreamingEndpoints가 포함될 수 있습니다. 대역폭 및 보안 요구 사항에 따라 하나 이상의 채널에 StreamingEndpoint 서비스를 전용으로 사용할 수 있습니다. 모든 StreamingEndpoint는 모든 채널에서 가져올 수 있습니다.

### <a name="program-event"></a>프로그램(이벤트)
[프로그램(이벤트)](https://docs.microsoft.com/rest/api/media/operations/program)에서는 라이브 스트림의 세그먼트 게시 및 저장을 제어할 수 있습니다. 채널은 프로그램(이벤트)을 관리합니다. 채널 및 프로그램 관계는 기존 미디어와 유사하여 채널에는 일정한 콘텐츠 스트림이 있고 프로그램 범위는 해당 채널에 있는 일부 시간 제한 이벤트로 지정됩니다.
**ArchiveWindowLength** 속성을 설정하여 프로그램에 대해 기록된 콘텐츠를 유지할 시간을 지정할 수 있습니다. 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다.

또한 ArchiveWindowLength는 클라이언트가 현재 라이브 위치에서 이전 시간을 검색할 수 있는 최대 시간을 나타냅니다. 프로그램은 지정된 시간 동안 실행되지만 기간 길이보다 늦는 콘텐츠는 계속 삭제됩니다. 또한 이 속성의 값은 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

각 프로그램(이벤트)은 자산에 연결됩니다. 프로그램을 게시하려면 연결된 자산에 대한 로케이터를 만들어야 합니다. 이 로케이터가 있으면 클라이언트에 제공할 수 있는 스트리밍 URL을 작성할 수 있습니다.

채널은 동시 실행 프로그램을 최대 세 개까지 지원하므로 동일한 들어오는 스트림의 보관 파일을 여러 개 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 예를 들어 비즈니스 요구 사항에 따라 6시간의 프로그램을 보관하고 마지막 10분만 브로드캐스트해야 할 수 있습니다. 이렇게 하려면 두 개의 동시 실행 프로그램을 만들어야 합니다. 한 프로그램은 6시간의 이벤트를 보관하도록 설정하고 프로그램은 게시하지 않습니다. 다른 프로그램은 10분 동안을 보관하도록 설정하고 프로그램을 게시합니다.

자세한 내용은 다음을 참조하세요.

* [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)
* [온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업](media-services-live-streaming-with-onprem-encoders.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

## <a name="protecting-content"></a>콘텐츠 보호
### <a name="dynamic-encryption"></a>동적 암호화
Azure Media Services를 사용하면 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Media Services를 사용하면 128비트 암호화 키를 사용하는 AES(Advanced Encryption Standard) 및 PlayReady 및/또는 Widevine DRM을 사용하는 공통 암호화(CENC)로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에 AES 키 및 PlayReady 라이선스를 전달하는 서비스를 제공합니다.

현재 암호화할 수 있는 스트리밍 형식은 HLS, MPEG DASH 및 부드러운 스트리밍입니다. 점진적 다운로드를 암호화할 수 없습니다.

Media Services에서 자산을 암호화하려는 경우 암호화 키(CommonEncryption 또는 EnvelopeEncryption)를 자산에 연결하고 해당 키에 대해 권한 부여 정책도 구성해야 합니다.

저장소에서 암호화된 자산을 스트리밍하려면 자산 배달 방법을 지정할 수 있도록 자산의 배달 정책을 구성해야 합니다.

플레이어에서 스트림을 요청하면 봉투 암호화(AES 사용) 또는 일반 암호화(PlayReady 또는 Widevine 사용)를 통해 사용자의 콘텐츠를 동적으로 암호화하기 위해 Media Services에서 지정된 키를 사용합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

### <a name="token-restriction"></a>토큰 제한
콘텐츠 키 권한 부여 정책에는 열기, 토큰 제한 또는 IP 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다. 토큰 제한 정책은 보안 토큰 서비스(STS)에 의해 발급된 토큰이 수반되어야 합니다. Media Services 지원 토큰에는 간단한 웹 토큰(SWT) 형식 및 JSON 웹 토큰(JWT) 형식의 토큰을 지원합니다. Media Services는 보안 토큰 서비스를 제공하지 않습니다. 사용자 지정 STS를 만들 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키(또는 라이선스)에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 요청된 키(또는 라이선스)를 클라이언트에 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키는 토큰이 서명된 키를 포함하며 발급자는 토큰을 발행하는 보안 토큰 서비스입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

자세한 내용은 다음 문서를 참조하세요.
- [콘텐츠 보호 개요](media-services-content-protection-overview.md)
- [AES-128로 보호](media-services-protect-with-aes128.md)
- [PlayReady/Widevine으로 보호](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>배달
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>동적 패키징
Media Services를 사용할 때는 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 집합을 원하는 형식으로 변환하는 것이 좋습니다.

### <a name="streaming-endpoint"></a>스트리밍 엔드포인트
StreamingEndpoint는 추가 배포를 위해 콘텐츠를 클라이언트 플레이어 애플리케이션이나 CDN(Content Delivery Network)에 직접 배달할 수 있는 스트리밍 서비스를 나타냅니다. 이제 Azure Media Services는 Azure CDN 통합을 제공합니다. 스트리밍 엔드포인트 서비스의 아웃바운드 스트림은 라이브 스트림 또는 Media Services 계정에 주문형 비디오 자산이 될 수 있습니다. Media Services 고객은 필요에 따라 **표준** 스트리밍 엔드포인트나 하나 이상의 **프리미엄** 스트리밍 엔드포인트를 선택합니다. 표준 스트리밍 엔드포인트는 대부분의 스트리밍 워크로드에 적합합니다. 

표준 스트리밍 엔드포인트는 대부분의 스트리밍 워크로드에 적합합니다. 표준 스트리밍 엔드포인트는 HLS, MPEG DASH 및 부드러운 스트리밍으로의 동적 패키징뿐만 아니라 Microsoft PlayReady, Google Widevine, Apple Fairplay 및 AES128에 대한 동적 암호화를 통해 거의 모든 디바이스에 콘텐츠를 제공할 수 있는 유연성을 제공합니다.  또한 Azure CDN 통합을 통해 매우 작은 대상 그룹에서 수천 명의 동시 시청자가 있는 매우 큰 대상 그룹으로 확장됩니다. 고급 워크로드가 있거나 스트리밍 용량 요구 사항이 표준 스트리밍 엔드포인트 처리량 목표에 맞지 않거나 증가하는 대역폭 요구를 처리하도록 스트리밍 엔드포인트 서비스의 용량을 제어하려는 경우 배율 단위(프리미엄 스트리밍 단위라고도 함)를 할당하는 것이 좋습니다.

동적 패키징 및/또는 동적 암호화를 사용하는 것이 좋습니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 

자세한 내용은 [이 항목](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.

기본적으로 Media Services 계정에 최대 2개의 스트리밍 엔드포인트가 있을 수 있습니다. 더 높은 한도를 요청하려면 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요

스트리밍 끝점이 실행 상태에 있을 때만 청구됩니다.

### <a name="asset-delivery-policy"></a>자산 배달 정책
Media Services 콘텐츠 배달 워크플로의 단계 중 하나를 구성 하 고 [자산 배달 정책을](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)스트리밍할 수 하려는 합니다. 자산 배달 정책은 어떤 스트리밍 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두)로 사용자의 자산을 동적으로 패키지할 지와 같은 사용자가 원하는 자산 배달 방법과 사용자의 자산을 동적으로 암호화할 지 여부 및 방법(봉투 또는 일반 암호화)를 Media Services에 알려줍니다.

저장소에서 암호화된 자산이 있는 경우 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거하고 지정된 배달 정책을 사용하여 콘텐츠를 스트리밍합니다. 예를 들어 AES(Advanced Encryption Standard) 암호화 키로 암호화된 자산을 배달하려면 정책 유형을 DynamicEnvelopeEncryption으로 설정합니다. 저장소 암호화를 제거하고 암호화 되지 않은 자산을 스트리밍하려면, 정책 유형을 NoDynamicEncryption로 설정하세요.

### <a name="progressive-download"></a>점진적 다운로드
점진적 다운로드를 사용하면 전체 파일이 다운로드되기 전에 미디어 재생을 시작할 수 있습니다. MP4 파일만 점진적으로 다운로드할 수 있습니다.

>[!NOTE]
>암호화된 자산을 점진적 다운로드에 사용할 수 있도록 하려면 해당 자산의 암호를 해독해야 합니다.

사용자에게 점진적 다운로드 ,URL을 제공하려면 먼저 OnDemandOrigin 로케이터를 만들어야 합니다. 로케이터를 만들면 자산에 대한 기본 경로가 제공됩니다. 그러면 MP4 파일의 이름을 추가해야 합니다. 예를 들면 다음과 같습니다.

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>스트리밍 URL
클라이언트로 콘텐츠를 스트리밍합니다. 사용자에게 스트리밍 URL을 제공하려면 먼저 OnDemandOrigin 로케이터를 만들어야 합니다. 로케이터를 만들면 스트리밍할 콘텐츠를 포함하는 자산에 대한 기본 경로가 제공됩니다. 그러나 이 콘텐츠를 스트리밍하려면 나중에 이 경로를 수정해야 합니다. 스트리밍 매니페스트 파일에 대한 전체 URL을 생성하려면 로케이터의 경로 값과 매니페스트(filename.ism) 파일 이름을 연결해야 합니다. 그런 다음 로케이터 경로에 /Manifest 및 적절한 형식(필요한 경우)을 추가합니다.

SSL 연결을 통해 콘텐츠를 스트리밍할 수도 있습니다. 이렇게 하려면 스트리밍 URL이 HTTPS로 시작해야 합니다. 현재 AMS는 사용자 지정 도메인을 사용하는 SSL을 지원하지 않습니다.  

>[!NOTE]
>콘텐츠를 배달하는 출발점이 될 스트리밍 엔드포인트가 2014년 9월 10일 이후에 만들어진 경우에만 SSL을 통해 스트리밍할 수 있습니다. 스트리밍 URL이 9월 10일 이후에 만들어진 스트리밍 엔드포인트를 기반으로 하는 경우 URL에는 "streaming.mediaservices.windows.net"(새 형식)이 포함됩니다. "origin.mediaservices.windows.net"(이전 형식)이 포함된 스트리밍 URL은 SSL을 지원하지 않습니다. URL이 이전 형식인 경우 SSL을 통해 스트리밍할 수 있도록 하려면 새 스트리밍 엔드포인트를 만듭니다. 새 스트리밍 엔드포인트를 기반으로 만들어진 URL을 사용하여 SSL을 통해 콘텐츠를 스트리밍합니다.

다음 목록에서는 다양한 스트리밍 형식에 대해 설명하고 예제를 제공합니다.

* 부드러운 스트리밍

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HLS(HTTP 라이브 스트리밍) V4

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HLS(HTTP 라이브 스트리밍) V3

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

