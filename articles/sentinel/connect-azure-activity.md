---
title: Azure 활동 데이터를 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure 활동 데이터를 Azure Sentinel 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d0cc13227bfe02594a57a7fb0ba8ee1cb3383d56
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125190"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure 활동 로그에서 데이터 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

로그를 스트리밍하려면 [Azure 활동 로그](../azure-monitor/platform/activity-logs-overview.md) 번의 클릭으로 Azure Sentinel에 있습니다. 활동 로그는 Azure에서 발생 하는 구독 수준 이벤트에 대 한 정보를 제공 하는 구독 로그입니다. 여기에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. 활동 로그를 통해 확인할 수 있습니다는 ' 무엇을, 누가, 언제 ' 대 한 모든 쓰기 작업 (PUT, POST, DELETE) 구독의 리소스에서 수행 합니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기 (GET) 작업 또는 클래식을 사용 하는 리소스에 대 한 작업이 없는 / "RDFE" 모델입니다. 


## <a name="prerequisites"></a>필수 조건

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자


## <a name="connect-to-azure-activity-log"></a>Azure 활동 로그에 연결

1. Azure Sentinel 선택 **데이터 커넥터** 클릭 하 고는 **Azure 활동 로그** 바둑판식으로 배열 합니다.

2. Azure 활동 로그 창에서 Azure Sentinel를 스트리밍 하려는 구독을 선택 합니다. 

3. **Connect**를 클릭합니다.

4. Log Analytics에서 관련 스키마를 사용 하 여 Azure 활동 경고에 대 한를 검색 **AzureActivity**합니다.


 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Azure 활동 로그 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
