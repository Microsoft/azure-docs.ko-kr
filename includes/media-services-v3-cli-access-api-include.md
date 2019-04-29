---
title: 포함 파일
description: 포함 파일
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309080"
---
## <a name="access-the-media-services-api"></a>Media Services API 액세스

Azure Media Services API에 연결하려면 Azure AD 서비스 주체 인증을 사용합니다. 다음 명령은 Azure AD 애플리케이션을 만들고 계정에 서비스 주체를 연결합니다. 반환된 값을 사용하여 애플리케이션을 구성해야 합니다.

스크립트를 실행하기 전에 `amsaccount` 및 `amsResourceGroup`을 이러한 리소스로 만들 때 선택한 이름으로 바꿀 수 있습니다. `amsaccount`는 서비스 주체를 연결하는 Azure Media Services 계정의 이름입니다.

다음 명령은 `json` 출력을 반환합니다.

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

이 명령은 다음과 비슷한 응답을 생성합니다.

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

응답에서 `xml`을 가져오려면 다음 명령을 사용합니다.

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
