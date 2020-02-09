---
title: 필수 매개 변수 수집
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 모든 Cognitive Services의 컨테이너에 대 한 매개 변수
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "73465911"
---
## <a name="gathering-required-parameters"></a>필수 매개 변수 수집

필요한 모든 Cognitive Services의 컨테이너에 대 한 세 가지 기본 매개 변수가 필요 합니다. EULA (최종 사용자 사용권 계약)는 `accept`값과 함께 제공 되어야 합니다. 또한 엔드포인트 URL과 API 키가 모두 필요 합니다.

### <a name="endpoint-uri-endpoint_uri"></a>엔드포인트 URI `{ENDPOINT_URI}`

**엔드포인트** URI 값은 해당 하는 인지 서비스 리소스의 Azure Portal *개요* 페이지에서 사용할 수 있습니다. *개요* 페이지로 이동 하 여 엔드포인트 위로 마우스를 이동 하면 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 아이콘이 표시 됩니다. 필요에 따라 복사 하 여 사용 합니다.

![나중에 사용할 엔드포인트 uri 수집](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>키 `{API_KEY}`

이 키는 컨테이너를 시작 하는 데 사용 되며 해당 하는 인지 서비스 리소스의 Azure Portal 키 페이지에서 사용할 수 있습니다. *키* 페이지로 이동 하 고 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 아이콘을 클릭 합니다.

![나중에 사용 하기 위해 두 키 중 하나를 가져옵니다.](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 이러한 구독 키는 인식 서비스 API에 액세스 하는 데 사용 됩니다. 키를 공유 하지 마세요. 예를 들어 Azure Key Vault를 사용 하 여 안전 하 게 저장 합니다. 또한 이러한 키를 정기적으로 다시 생성 하는 것이 좋습니다. API 호출을 수행 하는 데는 키가 하나만 필요 합니다. 첫 번째 키를 다시 생성 하는 경우 두 번째 키를 사용 하 여 서비스에 계속 액세스할 수 있습니다.