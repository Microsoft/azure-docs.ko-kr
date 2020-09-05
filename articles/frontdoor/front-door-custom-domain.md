---
title: 자습서 - Azure Front Door 구성에 사용자 지정 도메인 추가
description: 이 자습서에서는 사용자 지정 도메인을 Azure Front Door에 등록하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: f0d4ab9e3ecba8af1f6775389a4837e8c90eb14d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399772"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>자습서: Front Door에 사용자 지정 도메인 추가
이 자습서에서는 사용자 지정 도메인을 Front Door에 추가하는 방법을 알아봅니다. 애플리케이션 전송에 Azure Front Door를 사용하는 경우 고유한 도메인 이름을 최종 사용자 요청에 표시하려면 사용자 지정 도메인이 필요합니다. 볼 수 있는 도메인 이름이 있다면 고객에게 편리하고 브랜딩 목적상 유용합니다.

Front Door를 만든 후, 기본적으로 백 엔드에서 Front Door 콘텐츠를 전송하기 위해 기본 프런트 엔드 호스트(`azurefd.net`의 하위 도메인)가 URL에 포함됩니다(예: https:\//contoso.azurefd.net/activeusers.htm). 사용자 편의를 위해 Azure Front Door는 사용자 지정 도메인을 기본 호스트에 연결하는 옵션을 제공합니다. 이 옵션을 사용하면 URL에 Front Door 소유의 도메인 이름 대신 사용자 지정 도메인을 사용하여 콘텐츠를 전송합니다(예: https:\//www.contoso.com/photo.png). 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - CNAME DNS 레코드 만들기.
> - 사용자 지정 도메인을 Front Door와 연결합니다.
> - 사용자 지정 도메인 확인.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Front Door는 [punycode](https://en.wikipedia.org/wiki/Punycode) 문자가 있는 사용자 지정 도메인을 지원하지 **않습니다**. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서의 단계를 완료하려면 먼저 Front Door를 만들어야 합니다. 자세한 내용은 [빠른 시작: Front Door 만들기](quickstart-create-front-door.md)를 참조하세요.

사용자 지정 도메인이 없으면 먼저 도메인 공급자를 통해 구매해야 합니다. 예를 들어 [사용자 지정 도메인 이름 구매](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)를 참조하세요.

Azure를 사용하여 [DNS 도메인](https://docs.microsoft.com/azure/dns/dns-overview)을 호스트하는 경우 도메인 공급자의 DNS(Domain Name System)를 Azure DNS에 위임해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)을 참조하세요. 그렇지 않으면, 도메인 공급자를 사용하여 DNS 도메인을 처리하는 경우 [CNAME DNS 레코드 만들기](#create-a-cname-dns-record)로 계속 진행합니다.


## <a name="create-a-cname-dns-record"></a>CNAME DNS 레코드 만들기

Front Door에 사용자 지정 도메인을 사용하려면 먼저 도메인 공급 기업을 사용하여 Front Door의 기본 프런트 엔드 호스트(contoso.azurefd.net으로 가정)를 가리키는 CNAME(정식 이름) 레코드를 만들어야 합니다. CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드의 형식입니다. Azure Front Door의 경우 원본 도메인 이름은 사용자 지정 도메인 이름이고, 대상 도메인 이름은 Front Door 기본 호스트 이름입니다. Front Door가 사용자가 만든 CNAME 레코드를 확인하면, 원본 사용자 지정 도메인(예: www\.contoso.com)에 전달되는 트래픽은 지정된 대상 Front Door 기본 프런트 엔드 호스트(예: contoso.azurefd.net)로 라우팅됩니다. 

사용자 지정 도메인 및 해당 하위 도메인은 한 번에 하나의 Front Door에만 연결할 수 있습니다. 그러나 여러 CNAME 레코드를 사용하면 동일한 사용자 지정 도메인의 여러 하위 도메인을 여러 Front Door에 사용할 수 있습니다. 여러 하위 도메인이 있는 사용자 지정 도메인을 동일한 Front Door에 매핑할 수도 있습니다.


## <a name="map-the-temporary-afdverify-subdomain"></a>임시 afdverify 하위 도메인 매핑

프로덕션 중인 기존 도메인을 매핑할 경우 특별한 고려 사항이 있습니다. Azure Portal에서 사용자 지정 도메인을 등록하는 동안 도메인에 짧은 가동 중지 시간이 발생할 수 있습니다. 웹 트래픽이 중단하지 않게 하려면, 먼저 Azure cdnverify 하위 도메인을 포함한 Front Door 기본 프런트 엔드 호스트에 사용자 지정 도메인을 매핑하여 임시 CNAME 매핑을 만듭니다. 이 메서드를 사용하여 DNS 매핑이 발생하는 동안 사용자가 중단 없이 도메인에 액세스할 수 있습니다.

그렇지 않으면 처음으로 사용자 지정 도메인을 사용하고 프로덕션 트래픽이 실행되는 경우 직접 사용자 지정 도메인을 Front Door에 매핑할 수 있습니다. [영구 사용자 지정 도메인 매핑](#map-the-permanent-custom-domain)으로 진행합니다.

afdverify 하위 도메인에서 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 도메인 공급자의 웹 사이트에 로그인합니다.

2. 공급자의 설명서를 참조하거나 이름이 **도메인 이름**, **DNS** 또는 **이름 서버 관리**인 웹 사이트 영역을 검색하여 DNS 레코드 관리 페이지를 찾습니다. 

3. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본                    | Type  | 대상                     |
    |---------------------------|-------|---------------------------------|
    | afdverify. www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - 원본: afdverify 하위 도메인을 포함한 사용자 지정 도메인 이름을 afdverify. _&lt;custom domain name&gt;_ 형식으로 입력합니다. 예: afdverify. www.contoso.com.

    - 유형: *CNAME*를 입력합니다.

    - 대상: afdverify 하위 도메인을 포함한 기본 Front Door 프런트 엔드 호스트를 afdverify. _&lt;endpoint name&gt;_ .azurefd.net 형식으로 입력합니다. 예: afdverify.contoso.azurefd.net.

4. 변경 내용을 저장합니다.

예를 들어 GoDaddy 도메인 등록 기관에 대한 프로시저는 다음과 같습니다.

1. 로그인하고 사용하려는 사용자 지정 도메인을 선택합니다.

2. 도메인 섹션에서 **모두 관리**를 선택한 다음, **DNS** | **관리 영역**을 선택합니다.

3. **도메인 이름**에서 사용자 지정 도메인을 입력한 다음, **검색**을 선택합니다.

4. **DNS 관리** 페이지에서 **추가**를 선택한 다음, **형식** 목록에서 **CNAME**를 선택합니다.

5. CNAME 항목의 다음 필드를 완료합니다.

    - 유형: *CNAME*를 선택해 둡니다.

    - 호스트: afdverify 하위 도메인 이름을 포함하여 사용할 사용자 지정 도메인의 하위 도메인을 입력합니다. 예: afdverify. www.

    - 지시 대상: afdverify 하위 도메인 이름을 포함하여 기본 Front Door 프런트 엔드 호스트의 호스트 이름을 입력합니다. 예: afdverify.contoso.azurefd.net. 

    - TTL: *한 시간*을 선택된 상태로 둡니다.

6. **저장**을 선택합니다.
 
    CNAME 항목이 DNS 레코드 테이블에 추가됩니다.


## <a name="associate-the-custom-domain-with-your-front-door"></a>사용자 지정 도메인을 Front Door와 연결

사용자 지정 도메인을 등록한 후에는 Front Door에 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 사용자 지정 도메인에 매핑하려는 프런트 엔드 호스트가 포함된 Front Door로 이동합니다.
    
2. **Front Door 디자이너** 페이지에서 '+'를 클릭하여 사용자 지정 도메인을 추가합니다.
    
3. **사용자 지정 도메인**을 지정합니다. 

4. **프런트 엔드 호스트**의 경우 CNAME 레코드의 대상 도메인으로 사용할 프런트 엔드 호스트는 미리 채워져 있으며 Front Door *&lt;default hostname&gt;* .azurefd.net에서 파생됩니다. 이는 변경할 수 없습니다.

5. **사용자 지정 호스트 이름**의 경우 CNAME 레코드의 원본 도메인으로 사용하려면 하위 도메인을 포함하여 사용자 지정 도메인을 입력합니다. 예를 들어 www\.contoso.com 또는 cdn.contoso.com과 같습니다. afdverify 하위 도메인 이름을 사용하지 마세요.

6. **추가**를 선택합니다.

   Azure에서 입력한 사용자 지정 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다.

>[!WARNING]
> **반드시** Front Door의 각 프런트 엔드 호스트에(사용자 지정 도메인 포함) 기본 경로('/\*')가 연결된 회람 규칙이 있어야 합니다. 즉, 모든 회람 규칙에서 기본 경로('/\*')에 정의된 각 프런트 엔드 호스트의 회람 규칙이 하나 이상 있어야 합니다. 그렇지 않으면 최종 사용자 트래픽이 올바르게 라우팅되지 않을 수 있습니다.

## <a name="verify-the-custom-domain"></a>사용자 지정 도메인 확인

사용자 지정 도메인의 등록을 완료한 후에는 사용자 지정 도메인이 기본 Front Door 프런트 엔드 호스트를 참조하는지 확인합니다.
 
브라우저에서 사용자 지정 도메인을 사용하는 파일의 주소로 이동합니다. 예를 들어 사용자 지정 도메인이 robotics.contoso.com인 경우 캐시된 파일의 URL은 http:\//robotics.contoso.com/my-public-container/my-file.jpg URL과 비슷합니다. *&lt;Front Door host&gt;* .azurefd.net에서 직접 Front Door에 액세스할 때와 결과가 동일한지 확인합니다.


## <a name="map-the-permanent-custom-domain"></a>영구 사용자 지정 도메인 매핑

afdverify 하위 도메인이 Front Door에 성공적으로 매핑되었음을 확인한 경우(또는 프로덕션 중이 아닌 새 사용자 지정 도메인을 사용하는 경우) 사용자 지정 도메인을 기본 Front Door 프런트 엔드 호스트에 직접 매핑할 수 있습니다.

사용자 지정 도메인에 대한 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 도메인 공급자의 웹 사이트에 로그인합니다.

2. 공급자의 설명서를 참조하거나 이름이 **도메인 이름**, **DNS**, 또는 **이름 서버 관리**인 웹 사이트 부분을 검색하여 DNS 레코드 관리 페이지를 찾습니다. 

3. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본          | Type  | 대상           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azurefd.net |

   - 원본: 사용자 지정 도메인 이름(예: www\.contoso.com)을 입력합니다.

   - 유형: *CNAME*를 입력합니다.

   - 대상: 기본 Front Door 프런트 엔드 호스트를 입력합니다. format: _&lt;hostname&gt;_ .azurefd.net 형식이어야 합니다. 예: contoso.azurefd.net.

4. 변경 내용을 저장합니다.

5. 이전에 임시 afdverify 하위 도메인 CNAME 레코드를 만든 경우 삭제합니다. 

6. 프로덕션에서 이 사용자 지정 도메인을 처음으로 사용하는 경우 [사용자 지정 도메인을 Front Door와 연결](#associate-the-custom-domain-with-your-front-door) 및 [사용자 지정 도메인 확인](#verify-the-custom-domain)의 단계를 따릅니다.

예를 들어 GoDaddy 도메인 등록 기관에 대한 프로시저는 다음과 같습니다.

1. 로그인하고 사용하려는 사용자 지정 도메인을 선택합니다.

2. 도메인 섹션에서 **모두 관리**를 선택한 다음, **DNS** | **관리 영역**을 선택합니다.

3. **도메인 이름**에서 사용자 지정 도메인을 입력한 다음, **검색**을 선택합니다.

4. **DNS 관리** 페이지에서 **추가**를 선택한 다음, **형식** 목록에서 **CNAME**를 선택합니다.

5. CNAME 항목의 필드를 완료합니다.

    - 유형: *CNAME*를 선택해 둡니다.

    - 호스트: 사용할 사용자 지정 도메인의 하위 도메인을 입력합니다. 예: www 또는 프로필.

    - 지시 대상: Front Door의 기본 호스트 이름을 입력합니다. 예: contoso.azurefd.net. 

    - TTL: *한 시간*을 선택된 상태로 둡니다.

6. **저장**을 선택합니다.
 
    CNAME 항목이 DNS 레코드 테이블에 추가됩니다.

7. afdverify CNAME 레코드가 있는 경우 옆에 있는 연필 아이콘을 선택한 다음, 휴지통 아이콘을 선택합니다.

8. **삭제**를 선택하여 CNAME 레코드를 삭제합니다.


## <a name="clean-up-resources"></a>리소스 정리

위의 단계에서는 사용자 지정 도메인을 Front Door에 추가했습니다. 더 이상 Front Door를 사용자 지정 도메인과 연결하지 않으려면 다음과 같은 단계를 수행하여 사용자 지정 도메인을 제거할 수 있습니다.
 
1. Front Door 디자이너에서 제거하려는 사용자 지정 도메인을 선택합니다.

2. 사용자 지정 도메인의 팝업 메뉴에서 [삭제]를 클릭합니다.  

   사용자 지정 도메인은 엔드포인트에서 분리됩니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - CNAME DNS 레코드 만들기.
> - 사용자 지정 도메인을 Front Door와 연결합니다.
> - 사용자 지정 도메인 확인.