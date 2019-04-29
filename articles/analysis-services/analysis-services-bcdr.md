---
title: Azure Analysis Services 고가용성 | Microsoft 문서
description: Azure Analysis Services 고가용성 보장.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51a0f560a0e4b6ff791d5ed3f9f221eb2eeb9b4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036045"
---
# <a name="analysis-services-high-availability"></a>Analysis Services 고가용성

이 문서에서는 Azure Analysis Services 서버에 대한 고가용성을 보장하는 방법을 설명합니다. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>서비스가 중단된 동안 고가용성 보장

드문 경우지만 Azure 데이터 센터에서 가동 중단이 발생할 수 있습니다. 가동 중단이 발생하면 몇 분 내지 몇 시간 동안 지속될 수 있는 업무 중단이 발생합니다. 고가용성은 대부분 서버 중복을 통해 획득됩니다. Azure Analysis Services를 통해 하나 이상의 지역에서 추가적인 보조 서버를 만들어 중복을 획득할 수 있습니다. 중복 서버를 만들 경우 해당 서버의 데이터 및 메타데이터가 오프라인으로 전환된 지역의 서버와 동기화되도록 하려면 다음을 수행합니다.

* 다른 지역의 중복 서버에 모델을 배포합니다. 이 방법을 적용할 경우 모든 서버가 동기화되도록 기본 서버와 중복 서버에서 모두 병렬로 데이터를 처리해야 합니다.

* 기본 서버에서 데이터베이스를 [백업](analysis-services-backup.md)하고 중복 서버에서 복원합니다. 예를 들어 야간에 Azure Storage에 백업하는 작업을 자동화하고 다른 지역의 다른 중복 서버로 복원할 수 있습니다. 

어느 경우에나 기본 서버에서 작동 중단이 발생하면 다른 지역 데이터 센터의 서버에 연결하기 위해 보고 클라이언트에서 연결 문자열을 변경해야 합니다. 이 변경은 마지막 수단으로, 치명적인 지역 데이터 센터 작동 중단이 발생한 경우에만 고려해야 합니다. 모든 클라이언트에서 연결을 업데이트하기 전에 기본 서버를 호스트하는 데이터 센터 작동 중단 상태에서 다시 온라인 상태로 전환될 가능성이 높습니다. 

보고 클라이언트에서 연결 문자열을 변경하지 않기 위해 주 서버에 대한 서버 [별칭](analysis-services-server-alias.md)을 만들 수 있습니다. 주 서버가 다운되면 다른 지역에서 중복 서버를 가리키도록 별칭을 변경할 수 있습니다. 주 서버에서 엔드포인트 상태 검사를 코딩하여 서버 이름에 대한 별칭을 자동화할 수 있습니다. 상태 검사가 실패하는 경우 동일한 엔드포인트를 다른 지역의 중복 서버로 보낼 수 있습니다. 

## <a name="related-information"></a>관련 정보

[Backup 및 복원](analysis-services-backup.md)   
[Azure Analysis Services 관리](analysis-services-manage.md)   
[별칭 서버 이름](analysis-services-server-alias.md) 

