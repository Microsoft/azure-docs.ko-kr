---
title: Video Indexer 계정 관리
titlesuffix: Azure Media Services
description: 이 문서에는 Azure에 연결된 Video Indexer 계정을 관리하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 1515a026ae297a960f220a97449d2258c0b75e58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553608"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Azure에 연결된 Video Indexer 계정 관리

이 문서에서는 Azure 구독 및 Azure Media Services 계정에 연결된 Video Indexer 계정을 관리하는 방법에 대해 설명합니다.

> [!NOTE]
> 이 항목에서 설명하는 계정 구성 조정을 수행하려면 Video Indexer 계정 소유자여야 합니다.

## <a name="prerequisites"></a>필수 조건

[Azure에 연결](connect-to-azure.md)에서 설명한 대로 Video Indexer 계정을 Azure에 연결합니다. 

[필수 조건](connect-to-azure.md#prerequisites)을 따르고, 이 문서의 [고려 사항](connect-to-azure.md#considerations)을 검토하세요.

## <a name="examine-account-settings"></a>계정 설정 검사

이 섹션에서는 Video Indexer 계정의 설정을 검사합니다.

설정을 보려면 다음을 수행합니다.

1. 오른쪽 위 모서리에서 사용자 아이콘을 클릭하고 **설정**을 선택합니다.

    ![설정](./media/manage-account-connected-to-azure/select-settings.png)

2. **설정** 페이지에서 **계정** 탭을 선택합니다.

Video Indexer 계정이 Azure에 연결되면 다음 항목이 표시됩니다.

* 기본 Azure Media Services 계정의 이름
* 실행 및 큐 대기 중인 인덱싱 작업의 수
* 할당된 예약 단위의 유형 및 수

계정에서 일부를 조정할 필요가 있으면 **설정** 페이지에서 계정 구성과 관련된 오류 및 경고를 볼 수 있습니다. 메시지에는 변경해야 하는 Azure Portal의 정확한 위치에 대한 링크가 포함되어 있습니다. 자세한 내용은 뒤에 나오는 [오류 및 경고](#errors-and-warnings) 섹션을 참조하세요.

## <a name="auto-scale-reserved-units"></a>예약 단위 자동 크기 조정

**설정** 페이지에서 미디어 RU(예약 단위)의 자동 크기 조정을 설정할 수 있습니다. 옵션이 **켜기**인 경우 최대 RU 수를 할당하고, Video Indexer에서 RU를 자동으로 중지/시작하는지 확인할 수 있습니다. 이 옵션을 사용하면 유휴 시간에 대한 추가 비용을 지불하지 않고, 인덱싱 로드가 높을 때 인덱싱 작업이 완료될 때까지 오랫동안 기다리지 않아도 됩니다.

자동 크기 조정은 1RU 미만으로 축소되거나 Media Services 계정의 기본 제한을 초과하도록 확장되지는 않습니다. 제한을 늘리려면 서비스 요청을 만드세요. 할당량 및 제한 사항과 지원 티켓을 여는 방법에 대한 자세한 내용은 [할당량 및 제한 사항](../../media-services/previous/media-services-quotas-and-limitations.md)을 참조하세요.

![등록](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>오류 및 경고

계정에서 일부를 조정할 필요가 있으면 **설정** 페이지에서 계정 구성과 관련된 오류 및 경고를 볼 수 있습니다. 메시지에는 변경해야 하는 Azure Portal의 정확한 위치에 대한 링크가 포함되어 있습니다. 이 섹션에서는 오류 및 경고 메시지에 대한 자세한 정보를 제공합니다.

* Event Grid

    Azure Portal을 사용하여 EventGrid 리소스 공급자를 등록해야 합니다. [Azure Portal](https://portal.azure.com/)에서 **구독** > [구독] > **ResourceProviders** > **Microsoft.EventGrid**로 차례로 이동합니다. **등록됨** 상태가 아닌 경우 **등록**을 클릭합니다. 등록하는 데 몇 분 정도 걸립니다. 

* 스트리밍 엔드포인트

    기본 Media Services 계정에 시작됨 상태의 기본 **스트리밍 엔드포인트**가 있는지 확인합니다. 그렇지 않으면 이 Media Services 계정 또는 Video Indexer에서 비디오를 시청할 수 없습니다.

* 미디어 예약 단위 

    비디오를 인덱싱하려면 미디어 예약 단위를 Media Services 리소스에 할당해야 합니다. 최적의 인덱싱 성능을 위해 S3 예약 단위를 10개 이상 할당하는 것이 좋습니다. 가격 책정 정보는 [Media Services 가격](https://azure.microsoft.com/pricing/details/media-services/) 페이지의 FAQ 섹션을 참조하세요.   

## <a name="next-steps"></a>다음 단계

다음 지침에 따라 평가판 계정 및/또는 Azure에 연결된 Video Indexer 계정과 프로그래밍 방식으로 상호 작용할 수 있습니다. [API 사용](video-indexer-use-apis.md).

Azure에 연결할 때 사용한 것과 동일한 Azure AD 사용자를 사용해야 합니다.
