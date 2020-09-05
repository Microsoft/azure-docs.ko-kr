---
title: 최상위 Azure 구독 정보 내보내기
description: 계정과 연결된 모든 Azure 구독 ID를 보는 방법을 설명합니다.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b3b2e9b501f2ae103900a085e9b7a4b412efb78e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686839"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>최상위 구독 정보 내보내기 및 보기
사용자 자격 증명과 연결된 구독 ID 집합을 확인할 필요가 있으면 [Azure 계정 센터에서 구독 정보를 사용하여 .json 파일을 다운로드](https://account.azure.com/subscriptions/download)합니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

다운로드한 .json 파일에서 제공하는 정보는 다음과 같습니다.
- 전자 메일: 계정과 연결된 이메일 주소입니다.
- Puid: 결제 계정과 연결된 고유 식별자입니다.
- SubscriptionIds: 구독 ID별로 열거된 계정에 속한 구독 목록입니다.

### <a name="subscriptionsjson-sample"></a>subscriptions.json 샘플

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
