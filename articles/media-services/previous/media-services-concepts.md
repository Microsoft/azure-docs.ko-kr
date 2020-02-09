---
title: Azure Media Services 개념 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure Media Services 개념과 다른 문서에 대 한 링크에 대 한 간략 한 개요를 제공 합니다.
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
ms.openlocfilehash: 2126fed5231f2264ba9a0bbc13be9410bb8294da
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978835"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services 개념 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.

이 항목에서는 가장 중요 한 Media Services 개념에 대 한 개요를 제공 합니다.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>자산 및 저장소
### <a name="assets"></a>Objektumok
[자산은](https://docs.microsoft.com/rest/api/media/operations/asset) 디지털 파일 (비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 닫힌 캡션 파일 포함)과 이러한 파일에 대 한 메타 데이터를 포함 합니다. 디지털 파일을 자산에 업로드 한 후에는 Media Services 인코딩 및 스트리밍 워크플로에 사용할 수 있습니다.

자산은 Azure Storage 계정의 blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 blob으로 저장 됩니다. 페이지 blob은 Azure Media Services에서 지원 되지 않습니다.

콘텐츠를 업로드 하 고 자산에 저장할 미디어 콘텐츠를 결정할 때 다음 사항을 고려해 야 합니다.

* 자산에는 고유한 미디어 콘텐츠 인스턴스가 하나만 포함 되어야 합니다. 예를 들어 TV 에피소드, 영화 또는 광고의 단일 편집이 있습니다.
* 자산은 오디오 시각적 파일의 여러 변환이 나 편집을 포함 해서는 안 됩니다. 자산을 부적절 하 게 사용 하는 한 가지 예는 단일 프로덕션에서 하나 이상의 TV 에피소드, 보급 알림 또는 여러 카메라 각도를 자산 내에 저장 하려고 시도 하는 것입니다. 자산에 다중 오디오 시각적 파일의 변환이 나 편집을 여러 개 저장 하면 인코딩 작업을 전송 하는 데 문제가 발생할 수 있으며, 워크플로 뒷부분에서 자산의 전달이 스트리밍 및 보안 됩니다.  

### <a name="asset-file"></a>자산 파일
[Assetfile](https://docs.microsoft.com/rest/api/media/operations/assetfile) 은 blob 컨테이너에 저장 된 실제 비디오 또는 오디오 파일을 나타냅니다. 자산 파일은 항상 자산과 연결 되며 자산에는 하나 이상의 파일이 포함 될 수 있습니다. 자산 파일 개체가 blob 컨테이너의 디지털 파일과 연결 되어 있지 않으면 Media Services 인코더 작업이 실패 합니다.

**Assetfile** 인스턴스와 실제 미디어 파일은 서로 다른 두 개체입니다. AssetFile 인스턴스는 미디어 파일에 대 한 메타 데이터를 포함 하는 반면, 미디어 파일은 실제 미디어 콘텐츠를 포함 합니다.

미디어 서비스 Api를 사용 하지 않고 Media Services에 의해 생성 된 blob 컨테이너의 콘텐츠를 변경해 서는 안 됩니다.

### <a name="asset-encryption-options"></a>자산 암호화 옵션
업로드, 저장 및 배달 하려는 콘텐츠 형식에 따라 Media Services에서 선택할 수 있는 다양 한 암호화 옵션을 제공 합니다.

>[!NOTE]
>암호화가 사용 되지 않습니다. Ez az alapértelmezett érték. 이 옵션을 사용 하면 콘텐츠가 전송 중이거나 저장소에 저장 된 상태일 때 보호 되지 않습니다.

점진적 다운로드를 사용 하 여 MP4를 제공 하려는 경우이 옵션을 사용 하 여 콘텐츠를 업로드 합니다.

**Storageencrypted** -AES 256 비트 암호화를 사용 하 여 암호화 되지 않은 콘텐츠를 로컬에서 암호화 한 다음 암호화 된 상태로 저장 된 Azure Storage에 업로드 하려면이 옵션을 사용 합니다. 저장소 암호화로 보호 되는 자산은 인코딩 전에 자동으로 암호 해제 되어 암호화 된 파일 시스템에 저장 되며, 필요에 따라 새 출력 자산으로 다시 업로드 하기 전에 다시 암호화 됩니다. 저장소 암호화에 대 한 주요 사용 사례는 디스크에 저장 된 상태에서 강력한 암호화로 고품질의 입력 미디어 파일을 보호 하려는 경우입니다. 

저장소 암호화 된 자산을 배달 하려면 콘텐츠를 배달 하려는 방법을 알 수 Media Services 있도록 자산의 배달 정책을 구성 해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거 하 고 지정 된 배달 정책 (예: AES, PlayReady 또는 암호화 없음)을 사용 하 여 콘텐츠를 스트리밍합니다. 

**CommonEncryptionProtected** -Common Encryption 또는 playready drm (예: playready drm으로 보호 된 부드러운 스트리밍)을 사용 하 여 콘텐츠를 암호화 하거나 이미 암호화 된 콘텐츠를 업로드 하려는 경우이 옵션을 사용 합니다.

**EnvelopeEncryptionProtected** – AES(ADVANCED ENCRYPTION STANDARD) (AES)로 암호화 된 HLS (HTTP 라이브 스트리밍)를 보호 (또는 이미 보호 된 상태에서 업로드) 하려는 경우이 옵션을 사용 합니다. AES로 이미 암호화 된 HLS를 업로드 하는 경우 Transform Manager로 암호화 되어 있어야 합니다.

### <a name="access-policy"></a>Hozzáférési szabályzat
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) 는 자산에 대 한 액세스 권한 (예: 읽기, 쓰기 및 나열) 및 기간을 정의 합니다. 일반적으로 개체를 자산에 포함 된 파일에 액세스 하는 데 사용 되는 로케이터의 개체를 로케이터에 전달 합니다.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="blob-container"></a>Blobtároló
Blob 컨테이너는 blob 집합의 그룹화를 제공 합니다. Blob 컨테이너는 Media Services에서 액세스 제어를 위한 경계 지점과 자산에 대 한 SAS (공유 액세스 서명) 로케이터를 사용 합니다. Azure Storage 계정은 blob 컨테이너를 무제한으로 포함할 수 있습니다. Egy tároló korlátlan számú blob tárolására használható.

>[!NOTE]
> 미디어 서비스 Api를 사용 하지 않고 Media Services에 의해 생성 된 blob 컨테이너의 콘텐츠를 변경해 서는 안 됩니다.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>로케이터
[로케이터](https://docs.microsoft.com/rest/api/media/operations/locator)는 자산에 포함 된 파일에 액세스할 수 있는 진입점을 제공 합니다. 액세스 정책은 클라이언트가 지정 된 자산에 액세스할 수 있는 권한 및 기간을 정의 하는 데 사용 됩니다. 로케이터는 액세스 정책과 다 대 일 관계를 가질 수 있습니다. 이러한 경우에는 다른 로케이터가 동일한 사용 권한 및 기간 설정을 사용 하는 동안 다른 클라이언트에 다른 시작 시간 및 연결 유형을 제공할 수 있습니다. 그러나 Azure storage 서비스에 의해 설정 된 공유 액세스 정책 제한으로 인해 지정 된 자산과 연결 된 고유 로케이터는 한 번에 6 개 이상 사용할 수 없습니다. 

Media Services는 두 가지 유형의 로케이터를 지원 합니다. OnDemandOrigin locator는 미디어 (예: MPEG 대시, HLS 또는 부드러운 스트리밍)를 스트리밍하는 데 사용 되거나 Azure storage에서 미디어 파일을 업로드 하거나 다운로드 하는 데 사용 되는 미디어 및 SAS URL 로케이터를 점진적으로 다운로드 하는 데 사용 됩니다. 

>[!NOTE]
>OnDemandOrigin locator를 만들 때 list 권한 (AccessPermissions. List)을 사용 하면 안 됩니다. 

### <a name="storage-account"></a>Tárfiók
Azure Storage에 대 한 모든 액세스는 저장소 계정을 통해 수행 됩니다. 미디어 서비스 계정은 하나 이상의 저장소 계정에 연결할 수 있습니다. 계정에는 저장소 계정 당 총 크기가 500TB 미만으로 유지 되는 한, 무제한 수의 컨테이너가 포함 될 수 있습니다.  Media Services는 여러 저장소 계정을 관리 하 고 메트릭 또는 임의 배포에 따라 이러한 계정에 업로드 하는 동안 자산의 배포 부하를 분산 하는 데 사용할 수 있는 SDK 수준 도구를 제공 합니다. 자세한 내용은 [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx)작업을 참조 하세요. 

## <a name="jobs-and-tasks"></a>작업 및 태스크
[작업](https://docs.microsoft.com/rest/api/media/operations/job) 은 일반적으로 오디오/비디오 프레젠테이션 하나를 처리 (예: 인덱스 또는 인코드) 하는 데 사용 됩니다. 여러 비디오를 처리 하는 경우 인코딩할 각 비디오에 대 한 작업을 만듭니다.

작업에는 수행할 처리에 대 한 메타 데이터가 포함 됩니다. 각 작업에는 원자성 처리 태스크, 해당 입력 자산, 출력 자산, 미디어 프로세서 및 관련 설정을 지정 하는 하나 이상의 [태스크가](https://docs.microsoft.com/rest/api/media/operations/task)포함 됩니다. 작업 내의 태스크는 함께 연결 될 수 있습니다. 여기서 한 태스크의 출력 자산은 다음 태스크의 입력 자산으로 제공 됩니다. 이러한 방식으로 한 작업은 미디어 프레젠테이션에 필요한 모든 처리 작업을 포함할 수 있습니다.

## <a id="encoding"></a>인코딩이
Azure Media Services는 클라우드에서 미디어 인코딩에 대 한 여러 옵션을 제공 합니다.

Media Services 시작 하는 경우 코덱과 파일 형식 간의 차이점을 이해 하는 것이 중요 합니다.
코덱은 압축/압축 풀기 알고리즘을 구현 하는 소프트웨어 이며 파일 형식은 압축 된 비디오를 보관 하는 컨테이너입니다.

Media Services은 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩된 콘텐츠를 Media Services에서 지원 되는 스트리밍 형식 (MPEG 대시, HLS, 부드러운 스트리밍)으로 다시 패키지 하지 않고도 제공 하는 동적 패키징을 제공 합니다. 스트리밍 형식.

[동적 패키징을](media-services-dynamic-packaging-overview.md)활용 하려면 메자닌 (원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코드 하 고 시작 된 상태에서 하나 이상의 표준 또는 프리미엄 스트리밍 엔드포인트을 사용 해야 합니다.

Media Services은이 문서에서 설명 하는 다음과 같은 주문형 인코더를 지원 합니다.

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

지원 되는 인코더에 대 한 자세한 내용은 [인코더](media-services-encode-asset.md)를 참조 하세요.

## <a name="live-streaming"></a>Élő adások online közvetítése
Azure Media Services에서 채널은 라이브 스트리밍 콘텐츠를 처리 하기 위한 파이프라인을 나타냅니다. 채널은 다음 두 가지 방법 중 하나로 라이브 입력 스트림을 받습니다.

* 온-프레미스 라이브 인코더는 다중 비트 전송률 RTMP 또는 부드러운 스트리밍 (조각화 된 MP4)을 채널로 보냅니다. 다중 비트 전송률 부드러운 스트리밍를 출력 하는 다음과 같은 라이브 인코더를 사용할 수 있습니다. MediaExcel, Ateme, 커뮤니케이션, Envivio, Cisco 및 정령. 다음 라이브 인코더는 Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision 및 Tricaster 인코더를 출력 RTMP 합니다. 수집 스트림은 추가 트랜스 코딩 및 인코딩 없이 채널을 통과 합니다. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* RTMP 또는 부드러운 스트리밍 (조각화 된 MP4) 형식 중 하나인 단일 비트 전송률 스트림은 Media Services를 사용 하 여 라이브 인코딩을 수행할 수 있는 채널에 전송 됩니다. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

### <a name="channel"></a>채널
Media Services에서 [채널](https://docs.microsoft.com/rest/api/media/operations/channel)은 라이브 스트리밍 콘텐츠 처리를 담당 합니다. 채널은 라이브 코드 변환기 제공 하는 입력 엔드포인트 (수집 URL)을 제공 합니다. 채널은 라이브 코드 변환기에서 라이브 입력 스트림을 수신 하 고 하나 이상의 StreamingEndpoints를 통해 스트리밍할 수 있도록 합니다. 또한 채널에서는 추가 처리 및 배달 전에 스트림을 미리 보고 유효성을 검사 하는 데 사용 하는 미리 보기 엔드포인트 (미리 보기 URL)을 제공 합니다.

채널을 만들 때 수집 URL 및 미리 보기 URL을 가져올 수 있습니다. 이러한 Url을 가져오려면 채널이 시작 됨 상태에 있을 필요가 없습니다. 라이브 코드 변환기에서 채널로 데이터 푸시를 시작할 준비가 되 면 채널을 시작 해야 합니다. 라이브 코드 변환기에서 데이터 수집을 시작 하면 스트림을 미리 볼 수 있습니다.

각 Media Services 계정에는 여러 개의 채널, 여러 프로그램 및 여러 StreamingEndpoints가 포함 될 수 있습니다. 대역폭 및 보안 요구 사항에 따라 StreamingEndpoint 서비스를 하나 이상의 채널에 전용으로 사용할 수 있습니다. 모든 StreamingEndpoint는 모든 채널에서 가져올 수 있습니다.

### <a name="program-event"></a>프로그램 (이벤트)
[프로그램 (이벤트)](https://docs.microsoft.com/rest/api/media/operations/program) 을 사용 하면 라이브 스트림에서 세그먼트의 게시 및 저장을 제어할 수 있습니다. 채널은 프로그램 (이벤트)을 관리 합니다. 채널 및 프로그램 관계는 기존 미디어와 비슷하며, 채널에는 일정 한 콘텐츠 스트림이 있고 프로그램 범위는 해당 채널의 시간 제한 이벤트로 지정 됩니다.
**ArchiveWindowLength** 속성을 설정 하 여 프로그램에 대해 기록 된 콘텐츠를 유지할 시간을 지정할 수 있습니다. Ez az érték 5 perc és 25 óra közötti lehet.

또한 ArchiveWindowLength는 클라이언트가 현재 라이브 위치에서 이전 시간을 검색할 수 있는 최대 시간을 결정 합니다. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

각 프로그램 (이벤트)은 자산과 연결 됩니다. 프로그램을 게시 하려면 연결 된 자산에 대 한 로케이터를 만들어야 합니다. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

További információ eléréséhez lásd:

* [Azure Media Services에서 Live Encoding을 수행할 수 있도록 설정 된 채널 사용](media-services-manage-live-encoder-enabled-channels.md)
* [Helyszíni kódolóktól többszörös átviteli sebességű streameket fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Tartalom védelme
### <a name="dynamic-encryption"></a>동적 암호화
Azure Media Services를 사용 하면 저장소, 처리 및 배달을 통해 컴퓨터를 떠날 때부터 미디어를 보호할 수 있습니다. Media Services를 사용 하면 AES (AES(Advanced Encryption Standard)) (128 비트 암호화 키 사용) 및 PlayReady 및/또는 Widevtodrm을 사용 하는 CENC (일반 암호화)를 사용 하 여 동적으로 암호화 된 콘텐츠를 배달할 수 있습니다. 또한 Media Services는 인증 된 클라이언트에 AES 키 및 PlayReady 라이선스를 전달 하는 서비스를 제공 합니다.

현재는 HLS, MPEG 대시 및 부드러운 스트리밍 같은 스트리밍 형식을 암호화할 수 있습니다. 점진적 다운로드는 암호화할 수 없습니다.

Media Services 자산을 암호화 하려는 경우 암호화 키 (CommonEncryption 또는 EnvelopeEncryption)를 자산과 연결 하 고 키에 대 한 권한 부여 정책도 구성 해야 합니다.

저장소 암호화 된 자산을 스트리밍하려면 자산 배달 방법을 지정 하기 위해 자산의 배달 정책을 구성 해야 합니다.

플레이어에서 스트림을 요청 하면 Media Services는 지정 된 키를 사용 하 여 봉투 암호화 (AES 사용) 또는 일반 암호화 (PlayReady 또는 Widevine를 사용 하 여 콘텐츠를 동적으로 암호화 합니다. 스트림을 해독 하기 위해 플레이어는 키 배달 서비스에서 키를 요청 합니다. 사용자에 게 키를 가져올 수 있는 권한이 있는지 여부를 결정 하기 위해 서비스는 키에 대해 지정한 권한 부여 정책을 평가 합니다.

### <a name="token-restriction"></a>토큰 제한
콘텐츠 키 권한 부여 정책에는 열기, 토큰 제한 또는 IP 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. Media Services는 SWT (단순 웹 토큰) 형식 및 JSON Web Token (JWT) 형식의 토큰을 지원 합니다. Media Services는 보안 토큰 서비스를 제공 하지 않습니다. 사용자 지정 STS를 만들 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효 하 고 토큰의 클레임이 키 (또는 라이선스)에 대해 구성 된 클레임과 일치 하면 Media Services 키 배달 서비스에서 요청 된 키 (또는 라이선스)를 클라이언트에 반환 합니다.

토큰 제한 정책을 구성 하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정 해야 합니다. 기본 확인 키에는 토큰을 서명 하는 키가 포함 되어 있습니다. 발급자는 토큰을 발급 하는 보안 토큰 서비스입니다. 대상 (범위 라고도 함)은 토큰의 의도 또는 토큰에서 액세스 권한을 부여 하는 리소스에 대해 설명 합니다. Media Services 키 배달 서비스는 토큰의 이러한 값이 템플릿의 값과 일치 하는지 확인 합니다.

További információkért tekintse át a következő cikkeket:
- [콘텐츠 보호 개요](media-services-content-protection-overview.md)
- [AES-128을 사용 하 여 보호](media-services-protect-with-aes128.md)
- [PlayReady/Widevine 사용 하 여 보호](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>가치
### <a name="a-iddynamic_packagingdynamic-packaging"></a><a id="dynamic_packaging"/>동적 패키징
Media Services 사용 하는 경우에는 메자닌 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용 하 여 집합을 원하는 형식으로 변환 하는 것이 좋습니다.

### <a name="streaming-endpoint"></a>Streamvégpont
StreamingEndpoint는 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스 또는 추가 배포를 위해 CDN (Content Delivery Network)으로 Azure Media Services (이제 Azure CDN 통합을 제공 합니다.) 스트리밍 엔드포인트 서비스의 아웃 바운드 스트림은 라이브 스트림 이거나 Media Services 계정에 주문형 비디오 자산이 될 수 있습니다. A Media Services ügyfelei általában egy **standard** szintű streamvégpontot vagy egy vagy több **prémium** szintű streamvégpontot választanak, saját igényeiknek megfelelően. 표준 스트리밍 엔드포인트은 대부분의 스트리밍 워크 로드에 적합 합니다. 

A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. 표준 스트리밍 엔드포인트은 HLS, MPEG-2 및 부드러운 스트리밍에 대 한 동적 패키징을 비롯 하 여 Microsoft PlayReady, Google Widevine Apple Fairplay 및의 동적 암호화를 통해 모든 장치에 콘텐츠를 제공 하는 유연성을 제공 합니다. AES128.  또한 Azure CDN 통합을 통해 수천 명의 동시 시청자를 사용 하 여 매우 작고 많은 대상 그룹으로 확장 됩니다. 고급 작업이 있거나 스트리밍 용량 요구 사항이 표준 스트리밍 엔드포인트 처리량 목표에 맞지 않거나 증가 하는 대역폭 요구를 처리 하도록 StreamingEndpoint 서비스의 용량을 제어 하려는 경우에는 배율 단위 (프리미엄 스트리밍 단위 라고도 함)를 할당 합니다.

동적 패키징 및/또는 동적 암호화를 사용 하는 것이 좋습니다.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

További információ [ebben](media-services-portal-manage-streaming-endpoints.md) a témakörben érhető el.

기본적으로 Media Services 계정에 최대 2 개의 스트리밍 엔드포인트을 포함할 수 있습니다. 더 높은 한도를 요청 하려면 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조 하세요.

StreamingEndpoint가 실행 중 상태인 경우에만 요금이 청구 됩니다.

### <a name="asset-delivery-policy"></a>자산 배달 정책
Media Services 콘텐츠 배달 워크플로의 단계 중 하나는 스트리밍할 [자산의 배달 정책을](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)구성 하는 것입니다. 자산 배달 정책은 사용자의 자산 배달 방법 (예: MPEG 대시, HLS, 부드러운 스트리밍 또는 모두)을 동적으로 암호화할지 여부를 지정 하 여 자산 배달 방법을 Media Services에 알려 줍니다. 자산 및 방법 (봉투 (envelope) 또는 일반 암호화).

저장소에 암호화 된 자산이 있는 경우 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거 하 고 지정 된 배달 정책을 사용 하 여 콘텐츠를 스트리밍합니다. 예를 들어 AES(Advanced Encryption Standard) (AES) 암호화 키로 암호화 된 자산을 배달 하려면 정책 유형을 DynamicEnvelopeEncryption로 설정 합니다. Clear에서 저장소 암호화를 제거 하 고 자산을 스트리밍하려면 정책 유형을 NoDynamicEncryption으로 설정 합니다.

### <a name="progressive-download"></a>점진적 다운로드
점진적 다운로드를 사용 하면 전체 파일이 다운로드 되기 전에 미디어 재생을 시작할 수 있습니다. MP4 파일만 점진적으로 다운로드할 수 있습니다.

>[!NOTE]
>암호화 된 자산을 점진적 다운로드에 사용할 수 있도록 하려면 암호화 된 자산의 암호를 해독 해야 합니다.

사용자에 게 점진적 다운로드 Url을 제공 하려면 먼저 OnDemandOrigin 로케이터를 만들어야 합니다. 로케이터를 만들면 자산의 기본 경로가 제공 됩니다. 그런 다음 MP4 파일의 이름을 추가 해야 합니다. Példa:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>스트리밍 Url
클라이언트에 콘텐츠 스트리밍 사용자에 게 스트리밍 Url을 제공 하려면 먼저 OnDemandOrigin 로케이터를 만들어야 합니다. 로케이터를 만들면 스트리밍할 콘텐츠를 포함 하는 자산에 대 한 기본 경로를 제공 합니다. 그러나이 콘텐츠를 스트리밍하려면이 경로를 추가로 수정 해야 합니다. 스트리밍 매니페스트 파일에 대 한 전체 URL을 생성 하려면 로케이터의 경로 값과 매니페스트 (파일 이름) 파일 이름을 연결 해야 합니다. 그런 다음 로케이터 경로에/매니페스트와 적절 한 형식 (필요한 경우)을 추가 합니다.

SSL 연결을 통해 콘텐츠를 스트리밍할 수도 있습니다. 이렇게 하려면 스트리밍 Url이 HTTPS로 시작 하는지 확인 합니다. 현재 AMS는 사용자 지정 도메인을 사용 하는 SSL을 지원 하지 않습니다.  

>[!NOTE]
>콘텐츠를 배달 하는 스트리밍 엔드포인트이 2014 년 9 월 10 일 이후에 만들어진 경우에만 SSL을 통해 스트리밍할 수 있습니다. 스트리밍 Url이 9 월 10 일 이후에 만들어진 스트리밍 엔드포인트을 기반으로 하는 경우 URL에는 "streaming.mediaservices.windows.net" (새 형식)이 포함 됩니다. "Origin.mediaservices.windows.net" (이전 형식)이 포함 된 스트리밍 Url은 SSL을 지원 하지 않습니다. URL이 이전 형식인 경우 SSL을 통해 스트리밍할 수 있도록 하려면 새 스트리밍 엔드포인트을 만듭니다. 새 스트리밍 엔드포인트을 기반으로 만든 Url을 사용 하 여 SSL을 통해 콘텐츠를 스트리밍합니다.

다음 목록에서는 다양 한 스트리밍 형식에 대해 설명 하 고 예제를 제공 합니다.

* Smooth Streaming

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{스트리밍 엔드포인트 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = mpd)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* HLS (Apple HTTP 라이브 스트리밍) V4

{스트리밍 엔드포인트 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = m3u8-aapl-v3-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* HLS (Apple HTTP 라이브 스트리밍) V3

{스트리밍 엔드포인트 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = m3u8-aapl-v3-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

