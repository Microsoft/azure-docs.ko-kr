---
title: Azure portal을 사용 하 여 온-프레미스 인코더로 라이브 스트림 | Microsoft Docs
description: 이 자습서에서는 통과 배달을 위해 구성된 채널을 만드는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 9a8ab024443744f50482dd2ca1cfb33db43359e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463375"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Azure portal을 사용 하 여 온-프레미스 인코더로 라이브 스트리밍을 수행합니다
> [!div class="op_single_selector"]
> * [포털](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

이 자습서에서는 Azure 포털을 사용하여 통과 배달을 위해 구성된 **채널** 을 만드는 단계를 안내합니다. 

## <a name="prerequisites"></a>필수 조건
자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* Azure 계정. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
* Media Services 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.
* 웹캠. 예를 들어, [Telestream Wirecast encoder](https://www.telestream.net/wirecast/overview.htm)

다음 문서를 검토하는 것이 좋습니다.

* [Azure Media Services RTMP 지원 및 라이브 인코더](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Azure Media Services를 사용하는 라이브 스트리밍 개요](media-services-manage-channels-overview.md)
* [다중 비트 전송률 스트림을 만드는 온-프레미스 인코더를 사용한 라이브 스트리밍](media-services-live-streaming-with-onprem-encoders.md)

## <a id="scenario"></a>일반적인 라이브 스트리밍 시나리오

다음 단계에서는 통과 배달을 위해 구성된 채널을 사용하는 일반적인 라이브 스트리밍 애플리케이션을 만드는 것과 관련된 작업에 대해 설명합니다. 이 자습서에는 통과 채널 및 라이브 이벤트를 생성 및 관리하는 방법을 보여 줍니다.

> [!NOTE]
> 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 **실행** 상태에 있는지 확인합니다. 
    
1. 비디오 카메라를 컴퓨터에 연결합니다. <br/>설치 아이디어는 [간단하고 이동 가능한 이벤트 비디오 기어 설정]( https://link.medium.com/KNTtiN6IeT)을 확인하세요.
1. 다중 비트 전송률 RTMP 또는 조각화된 MP4 스트림을 출력하는 온-프레미스 라이브 인코더를 실행 및 구성합니다. 자세한 내용은 [Azure Media Services RTMP 지원 및 라이브 인코더](https://go.microsoft.com/fwlink/?LinkId=532824)를 참조하세요.<br/>또한 이 블로그를 확인하세요. [OBS를 사용한 라이브 스트리밍 프로덕션](https://link.medium.com/ttuwHpaJeT)
   
    이 단계는 채널을 만든 후에도 수행할 수 있습니다.
1. 통과 채널을 만들고 시작합니다.
1. 채널 수집 URL을 검색합니다. 
   
    수집 URL은 스트림을 채널로 보내기 위해 라이브 인코더를 통해 사용됩니다.
1. 채널 미리 보기 URL을 검색합니다. 
   
    이 URL을 사용하여 채널이 라이브 스트림을 제대로 받고 있는지 확인합니다.
1. 라이브 이벤트/프로그램을 만듭니다. 
   
    Azure 포털을 사용하는 경우 라이브 이벤트를 만들면 자산도 만들어집니다. 

1. 스트리밍 및 보관을 시작할 준비가 되었으면 이벤트/프로그램을 시작합니다.
1. 필요에 따라 라이브 인코더는 광고를 시작하라는 신호를 받을 수 있습니다. 광고는 출력 스트림에 삽입됩니다.
1. 이벤트 스트리밍 및 보관을 중지할 때마다 이벤트/프로그램을 중지합니다.
1. 이벤트/프로그램을 삭제하고 필요에 따라 자산을 삭제합니다.     

> [!IMPORTANT]
> 온-프레미스 인코더 및 통과 채널을 사용한 라이브 스트리밍과 관련된 개념 및 고려 사항에 대해 알아보려면 [다중 비트 전송률 스트림을 만드는 온-프레미스 인코더를 사용한 라이브 스트리밍](media-services-live-streaming-with-onprem-encoders.md)을 검토하세요.
> 
> 

## <a name="to-view-notifications-and-errors"></a>알림 및 오류를 보려면
Azure 포털에서 생성된 알림 및 오류를 보려면 알림 아이콘을 클릭합니다.

![공지](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>통과 채널 및 이벤트 만들기 및 시작
채널은 라이브 스트림에서 세그먼트의 게시 및 저장소를 제어할 수 있는 이벤트/프로그램과 연결되어 있습니다. 채널은 이벤트를 관리합니다. 

**보관 창** 길이를 설정하여 프로그램에 대해 기록된 콘텐츠를 유지할 시간을 지정할 수 있습니다. 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다. 또한 보관 창 길이는 클라이언트가 현재 라이브 위치에서 이전 시간을 검색할 수 있는 최대 시간을 나타냅니다. 이벤트는 지정된 시간 동안 실행되지만 기간 길이보다 늦는 콘텐츠는 계속 삭제됩니다. 또한 이 속성의 값은 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

각 이벤트는 자산에 연결됩니다. 이벤트를 게시하려면 연결된 자산에 대한 주문형 로케이터를 만들어야 합니다. 이 로케이터가 있으면 클라이언트에 제공할 수 있는 스트리밍 URL을 빌드할 수 있습니다.

채널은 동시 실행 이벤트를 최대 세 개까지 지원하므로 동일한 들어오는 스트림의 보관 파일을 여러 개 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 예를 들어 비즈니스 요구 사항에 따라 6시간의 프로그램을 보관하고 마지막 10분만 브로드캐스트해야 할 수 있습니다. 이렇게 하려면 두 개의 동시 실행 프로그램을 만들어야 합니다. 한 프로그램은 6시간의 이벤트를 보관하도록 설정하고 프로그램은 게시하지 않습니다. 다른 프로그램은 10분 동안을 보관하도록 설정하고 프로그램을 게시합니다.

기존 라이브 이벤트를 다시 사용해서는 안 됩니다. 대신, 각 이벤트에 대해 새 이벤트를 만들고 시작합니다.

스트리밍 및 보관을 시작할 준비가 되었으면 이벤트를 시작합니다. 이벤트 스트리밍 및 보관을 중지할 때마다 프로그램을 중지 합니다. 

보관된 콘텐츠를 삭제하려면 이벤트를 중단 및 삭제한 다음 연결된 자산을 삭제합니다. 자산을 이벤트에서 사용하는 경우 삭제할 수 없습니다. 이벤트를 먼저 삭제해야 합니다. 

이벤트를 중단 및 삭제한 다음에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트림할 수 있습니다.

보관된 콘텐츠를 보관하려는데 스트리밍에 사용할 수 있는 콘텐츠가 없는 경우 스트리밍 로케이터를 삭제합니다.

### <a name="to-use-the-portal-to-create-a-channel"></a>포털을 사용하여 채널을 만들려면
이 섹션에서는 통과 채널을 만들기 위해 **빠른 생성** 옵션을 사용하는 방법을 보여 줍니다.

통과 채널에 대한 자세한 내용은 [다중 비트 전송률 스트림을 만드는 온-프레미스 인코더를 사용한 라이브 스트리밍](media-services-live-streaming-with-onprem-encoders.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **라이브 스트리밍**을 클릭합니다. 
   
    ![시작](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    **라이브 스트리밍** 창이 나타납니다.
3. **빠른 생성** 을 클릭하여 RTMP 수집 프로토콜로 통과 채널을 만듭니다.
   
    **새 채널 만들기** 창이 나타납니다.
4. 새 채널 이름을 지정하고 **만들기**를 클릭합니다. 
   
    그러면 RTMP 수집 프로토콜을 사용하여 통과 채널이 만들어집니다.

## <a name="create-events"></a>이벤트 생성
1. 이벤트를 추가하려는 채널을 선택합니다.
2. **라이브 이벤트** 단추를 누릅니다.

![행사](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>수집 URL 가져오기
채널을 만든 후 라이브 인코더에 제공할 수집 URL을 가져올 수 있습니다. 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다.

![생성일](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>이벤트 보기
이벤트를 보려면 Azure 포털에서 **조사식** 을 클릭하거나 스트리밍 URL을 복사하고 선택한 플레이어를 사용합니다. 

![생성일](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

라이브 이벤트는 중지될 때 주문형 콘텐츠로 자동으로 변환합니다.

## <a name="clean-up"></a>정리
통과 채널에 대한 자세한 내용은 [다중 비트 전송률 스트림을 만드는 온-프레미스 인코더를 사용한 라이브 스트리밍](media-services-live-streaming-with-onprem-encoders.md)을 참조하세요.

* 채널에 있는 모든 이벤트/프로그램이 중지되었을 때만 채널을 중지할 수 있습니다.  채널이 중지되면 요금이 발생하지 않습니다. 채널을 다시 시작해야 하는 경우 채널의 수집 URL은 동일하므로 인코더를 다시 구성하지 않아도 됩니다.
* 채널에 있는 모든 이벤트가 삭제되었을 때만 채널을 삭제할 수 있습니다.

## <a name="view-archived-content"></a>보관된 콘텐츠 보기
이벤트를 중단 및 삭제한 다음에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트림할 수 있습니다. 자산을 이벤트에서 사용하는 경우 삭제할 수 없습니다. 이벤트를 먼저 삭제해야 합니다. 

자산을 관리하려면 **설정**을 선택하고 **자산**을 클릭합니다.

![자산](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

