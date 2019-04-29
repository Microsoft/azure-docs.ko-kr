---
title: Video Indexer에 대한 질문과 대답 - Azure
titlesuffix: Azure Media Services
description: Video Indexer에 대한 질문과 대답을 확인합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: e85beea4250b5ac5f4defd0b918786079d3dbe3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553694"
---
# <a name="frequently-asked-questions"></a>질문과 대답

이 문서에서는 Video Indexer에 대한 질문과 대답을 확인합니다.

## <a name="general-questions"></a>일반적인 질문

### <a name="what-is-video-indexer"></a>Video Indexer란?

Video Indexer는 Microsoft Azure Media Services의 일부인 AI 서비스입니다. Video Indexer는 비디오에서 심층 인사이트를 쉽게 추출할 수 있게 해주는 여러 기계 학습 모델의 오케스트레이션을 제공합니다. 정확한 고급 인사이트를 제공하기 위해 Video Indexer는 오디오, 음성, 시각적 개체 등 비디오의 여러 채널을 사용합니다. Video Indexer의 인사이트는 콘텐츠 검색 기능 및 접근성 향상, 새 수익 창출 기회 생성 또는 인사이트를 활용하는 새 경험 빌드와 같은 다양한 방법으로 사용될 수 있습니다. Video Indexer는 사용자 계정으로 모델을 테스트, 구성 및 사용자 지정하기 위한 웹 기반 인터페이스를 제공합니다. 개발자는 REST 기반 API를 사용하여 Video Indexer를 프로덕션 시스템에 통합할 수 있습니다. 

### <a name="what-can-i-do-with-video-indexer"></a>Video Indexer를 사용하여 무엇을 수행할 수 있나요?

Video Indexer가 미디어 파일에서 수행할 수 있는 일부 작업은 다음과 같습니다.

* 음성을 식별 및 추출하고 화자 식별
* 비디오에서 화면의 텍스트를 식별 및 추출
* 비디오 파일에서 개체 감지
* 오디오 트랙 및 비디오의 화면 텍스트에서 Microsoft와 같은 브랜드 식별
* 유명인의 데이터베이스 및 얼굴의 사용자 정의 데이터베이스에서 얼굴 검색 및 인식
* 논의되었으나 오디오 및 비디오 콘텐츠에서 반드시 언급될 필요는 없는 항목 추출
* 오디오 트랙의 선택 자막 또는 자막 만들기

자세한 내용 및 추가 Video Indexer 기능에 대한 내용은 [개요](video-indexer-overview.md)를 참조하세요.

### <a name="how-do-i-get-started-with-video-indexer"></a>Video Indexer를 시작하려면 어떻게 하나요?

Video Indexer에는 웹 기반 인터페이스에서 600분, API를 통해 2,400분을 제공하는 평가판 제품이 포함되어 있습니다. [Video Indexer 웹 기반 인터페이스에 로그인](https://www.videoindexer.ai/)한 다음, Azure 구독을 설정할 필요 없이 웹 ID를 사용하여 직접 사용해 볼 수 있습니다. 

비디오 및 오디오 파일을 대규모로 인덱싱하려면 Video Indexer를 유료 Microsoft Azure 구독에 연결할 수 있습니다. [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) 페이지에서 가격 책정에 대한 자세한 정보를 찾을 수 있습니다.

[시작](video-indexer-get-started.md)에서 시작에 대한 자세한 정보를 찾을 수 있습니다.

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Video Indexer를 사용하는 코딩 기술이 필요한가요?

**코딩할 필요 없이** Video Indexer 웹 기반 인터페이스를 사용하여 계정을 평가, 구성 및 관리할 수 있습니다.  더 복잡한 애플리케이션을 개발할 준비가 되면 [Video Indexer API](https://api-portal.videoindexer.ai/)를 통해 [Azure Logic Apps 또는 Azure Functions와 같은 서버리스 기술을 사용하여 Video Indexer를 사용자 고유의 애플리케이션, 웹 사이트 또는 사용자 지정 워크플로](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)에 통합할 수 있습니다.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Video Indexer를 사용하는 기계 학습 기술이 필요한가요?

아니요, Video Indexer는 여러 개의 기계 학습 모델을 하나의 파이프라인에 통합합니다. Video Indexer를 통해 비디오 또는 오디오 파일을 인덱싱하면 고객 쪽에서 기계 학습 기술이나 알고리즘에 대한 지식이 없어도 하나의 공유 타임라인에 추출된 전체 인사이트 세트가 검색됩니다.

### <a name="what-media-formats-does-video-indexer-support"></a>Video Indexer에서 지원하는 미디어 형식은 무엇인가요?

Video Indexer는 가장 일반적인 미디어 형식을 지원합니다. 자세한 내용은 [Azure Media Encoder Standard 형식](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) 목록을 참조하세요.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Video Indexer로 미디어를 어떻게 업로드하나요?

Video Indexer 웹 기반 포털에서 파일 업로드 대화 상자를 사용하거나 소스 파일을 직접 호스트하는 URL을 가리켜 미디어 파일을 업로드할 수 있습니다([예제](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4) 참조). iFrame 또는 embed 태그를 사용하여 미디어 콘텐츠를 호스트하는 URL은 작동하지 않습니다([예제](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) 참조). Video Indexer API를 사용하려면 URL 또는 바이트 배열을 통해 입력 파일을 지정해야 합니다. API를 사용하여 URL을 통해 업로드하는 작업은 10GB로 제한되지만 기간 제한은 없습니다. 자세한 내용은 이 [방법 가이드](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos)를 참조하세요.

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Video Indexer가 미디어에서 인사이트를 추출하는 데 얼마나 소요되나요?

Video Indexer API 및 Video Indexer 웹 기반 인터페이스를 사용하여 비디오 또는 오디오 파일을 인덱스하는 데 소요되는 시간은 파일 길이 및 품질, 파일에서 발견된 인사이트 수, 사용 가능한 [예약된 단위](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) 수, [스트리밍 엔드포인트](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview)의 활성화 여부 등과 같은 여러 매개 변수에 따라 달라집니다. 고유한 콘텐츠를 사용하여 몇 가지 테스트 파일을 실행하고 평균을 파악하는 것이 좋습니다.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>사용자 지정 워크플로를 만들어 Video Indexer를 사용한 프로세스를 자동화할 수 있나요?

예, Logic Apps, Flow 및 [Azure Functions](https://azure.microsoft.com/services/functions/)과 같은 서버리스 기술에 Video Indexer를 통합할 수 있습니다. [여기](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)에서 Video Indexer용 [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 및 [Flow](https://flow.microsoft.com/en-us/) 커넥터에 대한 자세한 정보를 확인할 수 있습니다. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>어떤 Azure 지역에서 Video indexer를 사용할 수 있나요?

[지역](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) 페이지에서 Video Indexer를 사용할 수 있는 Azure 지역을 찾을 수 있습니다.

### <a name="what-is-the-sla-for-video-indexer"></a>Video Indexer에 대한 SLA는 무엇인가요?

Azure Media Service의 SLA가 Video Indexer에 적용되며, [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) 페이지에서 확인할 수 있습니다. SLA는 Video Indexer 유료 계정에만 적용되며 평가판에는 적용되지 않습니다.

## <a name="privacy-questions"></a>개인 정보 관련 질문

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Video Indexer로 인덱싱된 비디오 및 오디오 파일이 저장되나요?

예, Video Indexer 웹 사이트 또는 API를 사용하여 Video Indexer에서 파일을 삭제하지 않는 한 비디오 및 오디오 파일이 저장됩니다. 평가판의 경우 인덱싱하는 비디오 및 오디오 파일이 미국 동부 Azure 지역에 저장됩니다. 평가판이 아닌 경우 비디오 및 오디오 파일이 Azure 구독의 스토리지 계정에 저장됩니다.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Video Indexer 포털에 저장된 파일을 삭제할 수 있나요?

예, 비디오 및 오디오 파일과 Video Indexer를 통해 해당 파일에서 추출된 모든 메타데이터 및 인사이트도 언제든지 삭제할 수 있습니다. Video Indexer에서 파일을 삭제하면 파일과 해당 메타데이터 및 인사이트가 Video Indexer에서 영구적으로 제거됩니다. 그러나 Azure Storage에 사용자 고유의 백업 솔루션을 구현한 경우에는 파일이 Azure Storage에 유지됩니다.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>내 Video Indexer 계정에 대한 사용자 액세스를 제어할 수 있나요?

예, 계정 관리자만 사용자를 해당 계정으로 초대하거나 초대를 취소하고, 편집 권한이 있는 사용자와 읽기 전용 권한을 가진 사용자를 할당할 수 있습니다.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Video Indexer를 통해 인덱싱 및 저장된 내 비디오 및 오디오 파일과 추출된 메타데이터 및 인사이트에 누가 액세스할 수 있나요?

개인 정보가 공개로 설정된 비디오 또는 오디오 콘텐츠는 비디오 또는 오디오 콘텐츠와 해당 인사이트에 대한 링크가 있는 모든 사용자가 액세스할 수 있습니다. 개인 정보가 비공개로 설정된 비디오 또는 오디오 콘텐츠는 비디오 또는 오디오 콘텐츠의 계정으로 초대된 사용자만 액세스할 수 있습니다. 콘텐츠의 개인 정보 설정은 Video Indexer가 추출하는 메타데이터 및 인사이트에도 적용됩니다. 비디오 또는 오디오 파일을 업로드할 때 개인 정보 설정을 할당합니다. 인덱싱한 후에 개인 정보 설정을 변경할 수도 있습니다.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Video Indexer를 통해 인덱싱 및 저장된 내 비디오 및 오디오 파일과 추출된 메타데이터 및 인사이트에 대해 Microsoft는 어떤 액세스 권한을 가지나요?

[Azure OST](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)(온라인 서비스 사용 약관)에 따라, 사용자가 콘텐츠를 완전히 소유하며 Microsoft는 OST 및 Microsoft 개인정보처리방침에 의거해서만 사용자 콘텐츠와 Video Indexer가 콘텐츠에서 추출하는 메타데이터 및 인사이트에 액세스합니다.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>내 Video Indexer 계정에 빌드한 사용자 지정 모델을 다른 계정에서 사용할 수 있나요?

 아니요, 계정에 만든 사용자 지정 모델은 다른 계정에서 사용할 수 없습니다. 현재 Video Indexer를 사용하면 사용자 지정 [브랜드](customize-brands-model-overview.md), [언어](customize-language-model-overview.md) 및 [개인](customize-person-model-overview.md) 모델을 계정에 빌드할 수 있습니다. 이러한 모델은 모델을 만든 계정에서만 사용할 수 있습니다.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Video Indexer에서 인덱싱된 콘텐츠는 Video Indexer를 사용하고 있는 Azure 지역 내에 유지되나요?

예, 콘텐츠 및 해당 인사이트는 여러 Azure 지역을 사용하는 수동 구성이 Azure 구독에 없는 한 Azure 지역 내에 유지됩니다. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Video Indexer에 대한 개인정보처리방침은 무엇인가요?

Video Indexer는 [Microsoft 개인정보처리방침](https://privacy.microsoft.com/privacystatement)이 적용됩니다. 개인정보처리방침은 Microsoft에서 처리하는 개인 데이터, Microsoft에서 개인 데이터를 처리하는 방법 및 Microsoft에서 개인 데이터를 처리하는 목적을 설명합니다. 개인 정보에 대해 자세히 알아보려면 [Microsoft 보안 센터](https://www.microsoft.com/trustcenter)를 방문하세요.

### <a name="what-certifications-does-video-indexer-have"></a>Video Indexer에는 어떤 인증이 있나요?

현재 Video Indexer에는 SOC 인증이 있습니다. Video Indexer의 인증을 검토하려면 [Microsoft 보안 센터](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure)를 참조하세요.

## <a name="api-questions"></a>API 질문

### <a name="what-apis-does-video-indexer-offer"></a>Video Indexer에서 제공하는 API는 무엇인가요?

Video Indexer API를 사용하여 인덱싱, 메타데이터 추출, 자산 관리, 변환, 포함, 모델 사용자 지정 등을 수행할 수 있습니다. Video Indexer API 사용 방법에 대한 자세한 내용은 [Video Indexer 개발자 포털](https://api-portal.videoindexer.ai/)을 참조하세요.

### <a name="what-client-sdks-does-video-indexer-offer"></a>Video Indexer에서 제공하는 클라이언트 SDK는 무엇인가요?

현재 제공되는 클라이언트 SDK는 없습니다. Video Indexer 팀이 SDK를 개발 중이며 곧 제공할 예정입니다.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Video Indexer의 API를 시작하려면 어떻게 하나요?

[자습서: Video Indexer API 시작](video-indexer-use-apis.md)을 따릅니다.

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Video Indexer API와 Azure Media Service v3 API 간의 차이점은 무엇인가요?

현재 Video Indexer API와 Azure Media Service v3 API에서 제공하는 기능이 일부 중복됩니다. [여기](compare-video-indexer-with-media-services-presets.md)에서 두 서비스를 비교하는 방법에 대한 자세한 내용을 확인할 수 있습니다.

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>API 액세스 토큰이란 무엇이며 왜 필요한가요?

Video Indexer API는 권한 부여 API 및 작업 API를 포함합니다. 권한 부여 API는 액세스 토큰을 제공하는 호출을 포함합니다. 작업 API에 대한 각 호출은 호출의 권한 부여 범위와 일치하는 액세스 토큰과 연결되어야 합니다.

보안상의 이유로 Video Indexer API를 사용하는 데 액세스 토큰이 필요합니다. 이렇게 하면 사용자 또는 사용자의 계정에 대한 액세스 권한이 있는 사용자에서 오는 모든 호출을 확인합니다. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>계정 액세스 토큰, 사용자 액세스 토큰, 비디오 액세스 토큰 사이의 차이점은 무엇인가요?

* 계정 수준 – 계정 수준 액세스 토큰을 사용하여 계정 수준 또는 비디오 수준에서 작업을 수행할 수 있습니다. 예를 들어 비디오 업로드, 모든 비디오 나열, 비디오 인사이트 가져오기 등이 있습니다.
* 사용자 수준 - 사용자 수준 액세스 토큰을 사용하여 사용자 수준에서 작업을 수행할 수 있습니다. 예를 들어 연결된 계정 가져오기가 있습니다.
* 비디오 수준 – 비디오 수준 액세스 토큰을 사용하여 특정 비디오 작업을 수행할 수 있습니다. 예를 들어 비디오 인사이트 가져오기, 자막 다운로드, 위젯 가져오기 등이 있습니다.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>얼마나 자주 새 액세스 토큰을 가져와야 하나요? 액세스 토큰은 언제 만료되나요?

액세스 토큰은 1시간마다 만료되므로 매 시간마다 새 액세스 토큰을 생성해야 합니다. 

## <a name="billing-questions"></a>대금 청구 관련 질문

### <a name="how-much-does-video-indexer-cost"></a>Video Indexer의 비용은 얼마인가요?

Video Indexer는 인덱싱하는 콘텐츠 입력 기간을 기반으로 하는 간단한 종량제 가격 책정 모델을 사용합니다. 추가 요금은 인코딩, 스트리밍, 스토리지, 네트워크 사용량 및 미디어 예약 단위에 대해 적용될 수 있습니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) 페이지를 참조하세요.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Video Indexer를 사용 하 여에 대 한 청구 하는 경우

인덱싱할 비디오를 보낼 때 사용자가 인덱싱을 비디오 분석, 오디오 분석 또는 2가지 모두로 정의합니다. 사용자가 인덱싱을 정의하면 어떤 SKU가 청구되는지 결정됩니다. 처리 중 심각한 수준의 오류가 발생하는 경우 오류 코드가 응답으로 반환됩니다. 이 경우 비용이 청구되지 않습니다.  심각한 오류는 코드의 버그 또는 서비스의 내부 종속성에서 발생한 심각한 오류로 인해 발생할 수 있습니다. 잘못된 식별 또는 인사이트 추출과 같은 오류는 심각한 오류로 간주되지 않으며 응답이 반환됩니다. 유효한(오류 코드가 아님) 응답이 반환되는 경우 비용이 청구됩니다.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Video Indexer는 평가판을 제공하나요?

예, Video Indexer는 전체 서비스 및 API 기능을 포함하는 평가판을 제공합니다. 웹 기반 인터페이스 사용자의 경우 600분, API 사용자의 경우 2,400분의 비디오 할당량이 있습니다. 

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md)
