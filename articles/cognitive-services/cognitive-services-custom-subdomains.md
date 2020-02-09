---
title: 사용자 지정 하위 도메인
titleSuffix: Azure Cognitive Services
description: 각 인지 서비스 리소스에 대한 사용자 지정 하위 도메인 이름은 Azure Portal, Azure Cloud Shell 또는 Azure CLI를 통해 생성 됩니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647689"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Cognitive Services에 대한 사용자 지정 하위 도메인 이름

Azure Cognitive Services [Azure Portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)또는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 통해 생성 된 각 리소스에 대해 사용자 지정 하위 도메인 이름을 사용 합니다. 특정 Azure 지역에 있는 모든 고객에 게 공통적인 지역 엔드포인트과 달리 사용자 지정 하위 도메인 이름은 리소스에 대해 고유 합니다. 사용자 지정 하위 도메인 이름은 인증을 위해 Azure AD (Azure Active Directory)와 같은 기능을 사용 하도록 설정 하는 데 필요 합니다.

## <a name="how-does-this-impact-existing-resources"></a>이 방법은 기존 리소스에 어떤 영향을 미칩니까?

2019 년 7 월 1 일 이전에 만든 Cognitive Services 리소스는 연결 된 서비스에 대한 지역 엔드포인트을 사용 합니다. 이러한 엔드포인트은 기존 및 새 리소스와 함께 작동 합니다.

사용자 지정 하위 도메인 이름을 활용 하기 위해 기존 리소스를 마이그레이션하려는 경우 Azure AD와 같은 기능을 사용 하도록 설정 하려면 다음 지침을 따르세요.

1. Azure Portal에 로그인 하 고 사용자 지정 하위 도메인 이름을 추가할 Cognitive Services 리소스를 찾습니다.
2. **개요** 블레이드에서 **사용자 지정 도메인 이름 생성**을 찾아 선택 합니다.
3. 그러면 리소스에 대해 고유한 사용자 지정 하위 도메인을 만드는 지침이 포함 된 패널이 열립니다.
   > [!WARNING]
   > 사용자 지정 하위 도메인 이름을 만든 후에는 변경할 수 **없습니다** .

## <a name="do-i-need-to-update-my-existing-resources"></a>기존 리소스를 업데이트 해야 하나요?

아니요. 지역 엔드포인트은 신규 및 기존 Cognitive Services에 대해 계속 작동 하 고 사용자 지정 하위 도메인 이름은 선택 사항입니다. 사용자 지정 하위 도메인 이름이 추가 된 경우에도 지역 엔드포인트은 계속 해 서 리소스를 사용 합니다.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>SDK에서 리소스에 대한 영역을 요청 하는 경우 어떻게 되나요?

> [!WARNING]
> 음성 서비스는 현재 사용자 지정 하위 도메인을 지원 **하지 않습니다** . 음성 서비스 및 관련 Sdk를 사용 하는 경우 지역 엔드포인트을 사용 하세요.

지역 엔드포인트과 사용자 지정 하위 도메인 이름은 모두 지원 되며 서로 바꿔 사용할 수 있습니다. 그러나 전체 엔드포인트이 필요 합니다.

지역 정보는 [Azure Portal](https://portal.azure.com)의 리소스에 대한 **개요** 블레이드에서 사용할 수 있습니다. 지역 엔드포인트의 전체 목록은 [지역 엔드포인트 목록이 있나요?](#is-there-a-list-of-regional-endpoints) 를 참조 하세요.

## <a name="are-custom-subdomain-names-regional"></a>사용자 지정 하위 도메인 이름이 지역 입니까?

예. 사용자 지정 하위 도메인 이름을 사용 하는 경우 Cognitive Services 리소스의 국가별 측면이 변경 되지 않습니다.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>사용자 지정 하위 도메인 이름에 대한 요구 사항은 무엇 인가요?

사용자 지정 하위 도메인 이름은 리소스에 대해 고유 합니다. 이름에는 영숫자 문자와 `-` 문자만 포함할 수 있습니다. 길이는 2 ~ 64 자 사이 여야 하며 `-`로 끝날 수 없습니다.

## <a name="can-i-change-a-custom-domain-name"></a>사용자 지정 도메인 이름을 변경할 수 있나요?

아니요. 사용자 지정 하위 도메인 이름을 만들고 리소스와 연결한 후에는 변경할 수 없습니다.

## <a name="can-i-reuse-a-custom-domain-name"></a>사용자 지정 도메인 이름을 다시 사용할 수 있나요?

각 사용자 지정 하위 도메인 이름은 고유 하므로 Cognitive Services 리소스에 할당 한 사용자 지정 하위 도메인 이름을 다시 사용 하기 위해 기존 리소스를 삭제 해야 합니다. 리소스를 삭제 한 후에 사용자 지정 하위 도메인 이름을 다시 사용할 수 있습니다.

## <a name="is-there-a-list-of-regional-endpoints"></a>지역 엔드포인트의 목록이 있나요?

예. Azure Cognitive Services 리소스에 사용할 수 있는 지역 엔드포인트의 목록입니다.

> [!NOTE]
> Translator Text API 및 Bing Search API는 전역 엔드포인트을 사용 합니다.

| 엔드포인트 유형 | 지역 | 엔드포인트 |
|---------------|--------|----------|
| 공용 | 전역 (Translator Text & Bing) | `https://api.cognitive.microsoft.com` |
| | 오스트레일리아 동부 | `https://australiaeast.api.cognitive.microsoft.com` |
| | 브라질 남부 | `https://brazilsouth.api.cognitive.microsoft.com` |
| | 캐나다 중부 | `https://canadacentral.api.cognitive.microsoft.com` |
| | 미국 중부 | `https://centralus.api.cognitive.microsoft.com` |
| | 동아시아 | `https://eastasia.api.cognitive.microsoft.com` |
| | 미국 동부 | `https://eastus.api.cognitive.microsoft.com` |
| | 미국 동부 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | 프랑스 중부 | `https://francecentral.api.cognitive.microsoft.com` |
| | 인도 중부 | `https://centralindia.api.cognitive.microsoft.com` |
| | 일본 동부 | `https://japaneast.api.cognitive.microsoft.com` |
| | 한국 중부 | `https://koreacentral.api.cognitive.microsoft.com` |
| | 미국 중북부 | `https://northcentralus.api.cognitive.microsoft.com` |
| | 북유럽 | `https://northeurope.api.cognitive.microsoft.com` |
| | 남아프리카 북부 | `https://southafricanorth.api.cognitive.microsoft.com` |
| | 미국 중남부 | `https://southcentralus.api.cognitive.microsoft.com` |
| | 동남아시아 | `https://southeastasia.api.cognitive.microsoft.com` |
| | 영국 남부 | `https://uksouth.api.cognitive.microsoft.com` |
| | 미국 중서부 | `https://westcentralus.api.cognitive.microsoft.com` |
| | 서유럽 | `https://westeurope.api.cognitive.microsoft.com` |
| | 미국 서부 | `https://westus.api.cognitive.microsoft.com` |
| | 미국 서부 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov 버지니아 | `https://virginia.api.cognitive.microsoft.us` |
| 중국 | 중국 동부 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 중국 북부 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>참고 항목

* [Cognitive Services은 무엇 인가요?](Welcome.md)
* [인증](authentication.md)
