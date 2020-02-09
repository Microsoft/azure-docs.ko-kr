---
title: Azure 데이터 공유를 모니터링 하는 방법
description: Azure 데이터 공유에서 초대 상태, 공유 구독 및 스냅숏 기록을 모니터링 하는 방법에 대해 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490513"
---
# <a name="monitor-azure-data-share"></a>Azure Data Share 모니터링  

이 문서에서는 Azure 데이터 공유를 사용 하 여 데이터 공유를 모니터링 하는 방법을 설명 합니다. 데이터 공급자는 데이터 공유 관계의 다양 한 측면을 모니터링할 수 있습니다. 데이터 소비자가 데이터 공유에 대한 초대를 수락 했는지 여부 및 공유 구독을 만들고 데이터 사용을 시작 했는지 여부와 같은 세부 정보는 모두 모니터링할 수 있습니다. 

데이터 소비자는 Azure 구독으로 트리거된 스냅숏을 모니터링할 수 있습니다. 

## <a name="monitor-invitation-status"></a>초대 상태 모니터링

보낸 공유-> 초대로 이동 하 여 데이터 공유 초대의 상태를 확인 합니다. 

![초대 상태](./media/invitation-status.png "초대 상태") 

초대는 다음과 같은 세 가지 상태가 될 수 있습니다.

* 보류 중-데이터 공유 받는 사람이 아직 초대를 수락 하지 않았습니다.
* 수락-데이터 공유 받는 사람이 초대를 수락 했습니다.
* 거부 됨-데이터 공유 받는 사람이 초대를 거부 했습니다.

> [!IMPORTANT]
> 이미 수락 된 후에는 초대를 삭제 하는 것은 액세스를 취소 하는 것과 동일 하지 않습니다. 이후 스냅숏이 데이터 소비자 저장소 계정에 복사 되는 것을 중지 하려면 *공유 구독* 탭을 통해 액세스를 취소 해야 합니다. 

## <a name="monitor-share-subscriptions"></a>공유 구독 모니터링

전송 된 공유로 이동 하 여 공유 구독의 상태를 확인 합니다. 구독 공유를 > 합니다. 그러면 초대를 승인한 후 데이터 소비자가 만든 활성 구독에 대한 세부 정보가 제공 됩니다. 공유 구독을 선택 하 고 *해지*를 선택 하 여 데이터 소비자에 대한 향후 업데이트를 중지할 수 있습니다. 

## <a name="snapshot-history"></a>스냅숏 기록 

기록 탭에서 데이터 소비자의 테넌트에 복사 된 스냅숏을 볼 수 있습니다. 각 스냅숏 간격의 빈도 및 기간을 모니터링할 수 있습니다. 

![스냅숏 기록](./media/sent-shares.png "스냅숏 기록") 

실행 시작 날짜를 클릭 하 여 각 스냅숏 실행에 대한 자세한 정보를 볼 수 있습니다. 

기본적으로 최대 30 일까 지 스냅숏 기록이 표시 됩니다. 30 일 이상 분량의 기록이 표시 되어야 하는 경우 모니터링-> 진단 설정으로 이동 하 고 진단 설정 **추가**를 선택 합니다. 이러한 로그를 저장할 저장소 계정을 선택 해야 합니다. 

![스냅숏 기록](./media/diagnostic-settings.png "진단 설정") 

## <a name="next-steps"></a>다음 단계 

[Azure 데이터 공유 용어](terminology.md) 에 대한 자세한 정보