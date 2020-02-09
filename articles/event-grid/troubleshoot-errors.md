---
title: Azure Event Grid-문제 해결 가이드
description: 이 문서에서는 오류 코드, 오류 메시지, 설명 및 권장 되는 작업 목록을 제공 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645075"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure Event Grid 오류 문제 해결
이 문제 해결 가이드에는 Azure Event Grid 오류 코드 목록, 오류 메시지, 설명 및 이러한 오류를 받을 때 수행 해야 하는 권장 작업이 나와 있습니다. 

## <a name="error-code-400"></a>오류 코드: 400
| 오류 코드 | 오류 메시지 | Description | 권장 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode 요청<br/>400 | 토픽 이름은 길이가 3 ~ 007e; 50 자 사이 여야 합니다. | 사용자 지정 토픽 이름 길이는 길이가 3 ~ 007e; 50 자 사이 여야 합니다. 토픽 이름에는 영숫자 문자, 숫자 및 '-' 문자만 사용할 수 있습니다. 또한 이름은 다음 예약어로 시작 하면 안 됩니다. <ul><li>Microsoft</li><li>EventGrid</li><li>시스템</li></ul> | 토픽 이름 요구 사항을 준수 하는 다른 항목 이름을 선택 합니다. |
| HttpStatusCode 요청<br/>400 | 도메인 이름은 길이가 3 ~ 007e; 50 자 사이 여야 합니다. | 도메인 이름의 길이는 3 ~ 007e; 50 자 사이 여야 합니다. 토픽 이름에는 영숫자 문자, 숫자 및 '-' 문자만 사용할 수 있습니다. 또한 이름은 다음 예약어로 시작 하면 안 됩니다.<ul><li>Microsoft</li><li>EventGrid</li><li>시스템</li> | 도메인 이름 요구 사항을 준수 하는 다른 도메인 이름을 선택 합니다. |
| HttpStatusCode 요청<br/>400 | 만료 시간이 잘못 되었습니다. | 이벤트 구독의 만료 시간은 이벤트 구독을 사용 중지할 시기를 결정 합니다. 이 값은 미래에 유효한 DateTime 값 이어야 합니다.| 이벤트 구독 만료 시간이 유효한 날짜/시간 형식 인지 확인 하 고 나중에 사용 하도록 설정 되어 있는지 확인 합니다. |

## <a name="error-code-409"></a>오류 코드: 409
| 오류 코드 | 오류 메시지 | Description | 권장 작업 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode <br/>409 | 지정 된 이름의 항목이 이미 있습니다. 다른 항목 이름을 선택 하십시오.   | 올바른 게시 작업을 위해 단일 Azure 지역에서 사용자 지정 토픽 이름이 고유 해야 합니다. 동일한 이름을 다른 Azure 지역에서 사용할 수 있습니다. | 항목에 대해 다른 이름을 선택 합니다. |
| HttpStatusCode <br/> 409 | 지정 된이 있는 도메인이 이미 있습니다. 다른 도메인 이름을 선택 하십시오. | 도메인 이름은 올바른 게시 작업을 보장 하기 위해 단일 Azure 지역에서 고유 해야 합니다. 동일한 이름을 다른 Azure 지역에서 사용할 수 있습니다. | 도메인에 대해 다른 이름을 선택 하십시오. |
| HttpStatusCode<br/>409 | 할당량 한도에 도달 했습니다. 이러한 제한에 대한 자세한 내용은 [Azure Event Grid 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)을 참조 하세요.  | 각 Azure 구독에는 사용할 수 있는 Azure Event Grid 리소스 수가 제한 되어 있습니다. 이 할당량의 일부 또는 전부를 초과 하 여 리소스를 더 이상 만들 수 없습니다. |    현재 리소스 사용량을 확인 하 고 필요 하지 않은 모든 리소스를 삭제 합니다. 할당량을 늘려야 하는 경우 필요한 정확한 리소스 수와 함께 [aeg@microsoft.com](mailto:aeg@microsoft.com) 에 전자 메일을 보냅니다. |


## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid) 에 문제를 게시 하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
