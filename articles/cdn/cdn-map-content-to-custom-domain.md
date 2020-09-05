---
title: 자습서 - Azure CDN 엔드포인트에 사용자 지정 도메인 추가 | Microsoft Docs
description: 이 자습서를 사용하여 도메인 이름이 URL에 표시되도록 Azure Content Delivery Network 엔드포인트에 사용자 지정 도메인을 추가합니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 4ca601a3f149d8287137265665379d407d10b988
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192699"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>자습서: Azure CDN 엔드포인트에 사용자 지정 도메인 추가
이 자습서에서는 Azure CDN(Content Delivery Network) 엔드포인트에 사용자 지정 도메인을 추가하는 방법을 보여줍니다. CDN 엔드포인트를 사용하여 콘텐츠를 제공할 때 고유한 도메인 이름을 CDN URL에 표시하려는 경우 사용자 지정 도메인이 필요합니다. 볼 수 있는 도메인 이름이 있다면 고객에게 편리하고 브랜딩 목적상 유용합니다. 

프로필에서 CDN 엔드포인트를 만든 후에 azureedge.net의 하위 도메인인 엔드포인트 이름은 기본적으로 CDN 콘텐츠를 배달하는 URL에 포함됩니다(예: https:\//contoso.azureedge.net/photo.png). 편의상 Azure CDN은 CDN 엔드포인트와 사용자 지정 도메인을 연결하는 옵션을 제공합니다. 이 옵션을 사용하면 엔드포인트 이름 대신 URL로 사용자 지정 도메인을 사용하여 콘텐츠를 배달합니다(예: https:\//www.contoso.com/photo.png). 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - CNAME DNS 레코드 만들기.
> - CDN 엔드포인트와 사용자 지정 도메인 연결.
> - 사용자 지정 도메인 확인.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 단계를 완료하기 전에 먼저 CDN 프로필 및 하나 이상의 CDN 엔드포인트를 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기](cdn-create-new-endpoint.md)를 참조하세요.

사용자 지정 도메인이 없으면 먼저 도메인 공급자를 통해 구매해야 합니다. 예를 들어 [사용자 지정 도메인 이름 구매](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)를 참조하세요.

Azure를 사용하여 [DNS 도메인](https://docs.microsoft.com/azure/dns/dns-overview)을 호스트하는 경우 도메인 공급자의 DNS(Domain Name System)를 Azure DNS에 위임해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)을 참조하세요. 그렇지 않으면, 도메인 공급자를 사용하여 DNS 도메인을 처리하는 경우 [CNAME DNS 레코드 만들기](#create-a-cname-dns-record)로 계속 진행합니다.


## <a name="create-a-cname-dns-record"></a>CNAME DNS 레코드 만들기

Azure CDN 엔드포인트에 사용자 지정 도메인을 사용하려면 먼저 해당 도메인 공급자를 통해 CNAME(Canonical Name) 레코드를 만들어서 CDN 엔드포인트를 가리켜야 합니다. CNAME 레코드는 원본 도메인을 대상 도메인 이름에 매핑하는 DNS 레코드의 형식입니다. Azure CDN의 경우 원본 도메인 이름은 사용자 지정 도메인 이름이고, 대상 도메인 이름은 CDN 엔드포인트 호스트 이름입니다. Azure CDN에서 사용자가 만든 CNAME 레코드를 확인하면 원본 사용자 지정 도메인(예: www\.contoso.com)에 전달되는 트래픽이 지정된 대상 CDN 엔드포인트 호스트 이름(예: contoso.azureedge.net)으로 라우팅됩니다. 

사용자 지정 도메인 및 해당 하위 도메인은 한 번에 하나의 단일 엔드포인트와 연결될 수 있습니다. 그러나 여러 CNAME 레코드를 사용하여 Azure 서비스 엔드포인트에 동일한 사용자 지정 도메인의 다른 하위 도메인을 사용할 수 있습니다. 여러 하위 도메인을 포함한 사용자 지정 도메인을 동일한 CDN 엔드포인트에 매핑할 수도 있습니다.

> [!NOTE]
> Azure DNS를 도메인 공급 기업으로 사용하는 경우 사용자 지정 도메인에 모든 별칭 레코드 종류를 사용할 수 있습니다. 이 연습에서는 CNAME 레코드 종류를 사용합니다. A 또는 AAAA 레코드 종류를 사용하는 경우 CNAME을 선택한 레코드 종류로 바꾸면서 아래의 동일한 단계를 수행합니다. 별칭 레코드를 사용하여 루트 도메인을 사용자 지정 도메인으로 추가하고 TLS를 사용하도록 설정하는 경우 [이 문서](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint)에서 설명한 대로 수동 유효성 검사를 사용해야 합니다. 자세한 내용은 [Azure CDN 엔드포인트로 지점 영역 Apex 지시](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints)를 참조하세요.

## <a name="map-the-temporary-cdnverify-subdomain"></a>임시 cdnverify 하위 도메인 매핑

프로덕션 중인 기존 도메인을 매핑할 경우 특별한 고려 사항이 있습니다. Azure Portal에서 사용자 지정 도메인을 등록하는 동안 도메인에 짧은 가동 중지 시간이 발생할 수 있습니다. 웹 트래픽을 중단하지 않도록 방지하려면 먼저 Azure cdnverify 하위 도메인을 포함한 CDN 엔드포인트 호스트 이름에 사용자 지정 도메인을 매핑하여 임시 CNAME 매핑을 만듭니다. 이 메서드를 사용하여 DNS 매핑이 발생하는 동안 사용자가 중단 없이 도메인에 액세스할 수 있습니다. 

그렇지 않으면 처음으로 사용자 지정 도메인을 사용하고 프로덕션 트래픽이 실행되는 경우 직접 사용자 지정 도메인을 CDN 엔드포인트에 매핑할 수 있습니다. [영구 사용자 지정 도메인 매핑](#map-the-permanent-custom-domain)으로 진행합니다.

cdnverify 하위 도메인에서 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 도메인 공급자의 웹 사이트에 로그인합니다.

2. 공급자의 설명서를 참조하거나 이름이 **도메인 이름**, **DNS** 또는 **이름 서버 관리**인 웹 사이트 영역을 검색하여 DNS 레코드 관리 페이지를 찾습니다. 

3. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본                    | Type  | 대상                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - 원본: cdnverify 하위 도메인을 포함한 사용자 지정 도메인 이름을 cdnverify.&lt;사용자 지정 도메인 이름&gt; 형식으로 입력합니다. 예: cdnverify. www.contoso.com

    - 유형: *CNAME*를 입력합니다.

    - 대상: cdnverify 하위 도메인을 비롯한 다음과 같은 형식의 CDN 엔드포인트 호스트 이름을 입력합니다. cdnverify. _&lt;엔드포인트 이름&gt;_ .azureedge.net 예: cdnverify.contoso.azureedge.net

4. 변경 내용을 저장합니다.

예를 들어 GoDaddy 도메인 등록 기관에 대한 프로시저는 다음과 같습니다.

1. 로그인하고 사용하려는 사용자 지정 도메인을 선택합니다.

2. 도메인 섹션에서 **모두 관리**를 선택한 다음, **DNS** | **관리 영역**을 선택합니다.

3. **도메인 이름**에서 사용자 지정 도메인을 입력한 다음, **검색**을 선택합니다.

4. **DNS 관리** 페이지에서 **추가**를 선택한 다음, **형식** 목록에서 **CNAME**를 선택합니다.

5. CNAME 항목의 다음 필드를 완료합니다.

    ![CNAME 항목](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - 유형: *CNAME*를 선택해 둡니다.

    - 호스트: cdnverify 하위 도메인 이름을 비롯해 사용할 사용자 지정 도메인의 하위 도메인을 입력합니다. 예: cdnverify.www

    - 지시 대상: cdnverify 하위 도메인 이름을 비롯한 CDN 엔드포인트의 호스트 이름을 입력합니다. 예: cdnverify.contoso.azureedge.net 

    - TTL: *1시간*을 선택해 둡니다.

6. **저장**을 선택합니다.
 
    CNAME 항목이 DNS 레코드 테이블에 추가됩니다.

    ![DNS 레코드 표](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>CDN 엔드포인트와 사용자 지정 도메인 연결

사용자 지정 도메인을 등록한 후에 CDN 엔드포인트에 추가할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 사용자 지정 도메인에 매핑하려는 엔드포인트를 포함하는 CDN 프로필로 이동합니다.
    
2. **CDN 프로필** 페이지에서 사용자 지정 도메인과 연결할 CDN 엔드포인트를 선택합니다.

   **엔드포인트** 페이지가 열립니다.
    
3. **사용자 지정 도메인**을 선택합니다. 

   ![CDN 사용자 지정 도메인 단추](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   **사용자 지정 도메인 추가** 페이지가 열립니다.

4. **엔드포인트 호스트 이름**의 경우 CNAME 레코드의 대상 도메인으로 사용할 엔트포인트 호스트 이름이 *&lt;endpoint hostname&gt;* .azureedge.net 같은 CDN 엔드포인트 URL에서 파생되고 미리 채워집니다. 이는 변경할 수 없습니다.

5. **사용자 지정 호스트 이름**의 경우 CNAME 레코드의 원본 도메인으로 사용하려면 하위 도메인을 포함하여 사용자 지정 도메인을 입력합니다. 예를 들어 www\.contoso.com 또는 cdn.contoso.com과 같습니다. cdnverify 하위 도메인 이름을 사용하지 마십시오.

   ![CDN 사용자 지정 도메인 대화 상자](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. **추가**를 선택합니다.

   Azure에서 입력한 사용자 지정 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다. 

   새 사용자 지정 도메인 설정이 모든 CDN 에지 노드에 전파되려면 다소 시간이 걸릴 수 있습니다. 
    - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
    - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
    - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다.   


## <a name="verify-the-custom-domain"></a>사용자 지정 도메인 확인

사용자 지정 도메인의 등록을 완료한 후에는 사용자 지정 도메인이 CDN 엔드포인트를 참조하는지 확인합니다.
 
1. 엔드포인트에 캐시된 공용 콘텐츠가 있는지 확인합니다. 예를 들어 CDN 엔드포인트가 스토리지 계정과 연결되어 있는 경우 Azure CDN은 공용 컨테이너에 콘텐츠를 캐시합니다. 사용자 지정 도메인을 테스트하려면 컨테이너가 공용 액세스를 허용하도록 설정되고 파일을 하나 이상 포함하는지 확인합니다.

2. 브라우저에서 사용자 지정 도메인을 사용하는 파일의 주소로 이동합니다. 예를 들어, 사용자 지정 도메인이 `www.contoso.com`인 경우 캐시된 파일의 URL은 URL `http://www.contoso.com/my-public-container/my-file.jpg`와 유사하게 됩니다. 결과가 *&lt;endpoint hostname&gt;* .azureedge.net에서 CDN 엔드포인트에 직접 액세스하는 경우와 동일한지 확인합니다.


## <a name="map-the-permanent-custom-domain"></a>영구 사용자 지정 도메인 매핑

cdnverify 하위 도메인이 엔드포인트에 성공적으로 매핑되었음을 확인한 경우(또는 프로덕션 중이 아닌 새 사용자 지정 도메인을 사용하는 경우) 사용자 지정 도메인을 직접 CDN 엔드포인트 호스트 이름에 매핑할 수 있습니다.

사용자 지정 도메인에 대한 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 도메인 공급자의 웹 사이트에 로그인합니다.

2. 공급자의 설명서를 참조하거나 이름이 **도메인 이름**, **DNS**, 또는 **이름 서버 관리**인 웹 사이트 부분을 검색하여 DNS 레코드 관리 페이지를 찾습니다. 

3. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본          | Type  | 대상           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - 원본: 사용자 지정 도메인 이름(예: www\.contoso.com)을 입력합니다.

   - 유형: *CNAME*를 입력합니다.

   - 대상: CDN 엔드포인트 호스트 이름을 입력합니다. 다음과 같은 형식이어야 합니다. _&lt;엔드포인트 이름&gt;_ .azureedge.net 예: contoso.azureedge.net

4. 변경 내용을 저장합니다.

5. 임시 cdnverify 하위 도메인 CNAME 레코드를 이전에 만든 경우 삭제합니다. 

6. 프로덕션에서 이 사용자 지정 도메인을 처음으로 사용하는 경우 [CDN 엔드포인트와 사용자 지정 도메인 연결](#associate-the-custom-domain-with-your-cdn-endpoint) 및 [사용자 지정 도메인 확인](#verify-the-custom-domain)의 단계에 따릅니다.

예를 들어 GoDaddy 도메인 등록 기관에 대한 프로시저는 다음과 같습니다.

1. 로그인하고 사용하려는 사용자 지정 도메인을 선택합니다.

2. 도메인 섹션에서 **모두 관리**를 선택한 다음, **DNS** | **관리 영역**을 선택합니다.

3. **도메인 이름**에서 사용자 지정 도메인을 입력한 다음, **검색**을 선택합니다.

4. **DNS 관리** 페이지에서 **추가**를 선택한 다음, **형식** 목록에서 **CNAME**를 선택합니다.

5. CNAME 항목의 필드를 완료합니다.

    ![CNAME 항목](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - 유형: *CNAME*를 선택해 둡니다.

    - 호스트: 사용할 사용자 지정 도메인의 하위 도메인을 입력합니다. 예: www 또는 cdn

    - 지시 대상: CDN 엔드포인트의 호스트 이름을 입력합니다. 예: contoso.azureedge.net 

    - TTL: *1시간*을 선택해 둡니다.

6. **저장**을 선택합니다.
 
    CNAME 항목이 DNS 레코드 테이블에 추가됩니다.

    ![DNS 레코드 표](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. cdnverify CNAME 레코드가 있는 경우 옆에 있는 연필 아이콘을 선택한 다음, 휴지통 아이콘을 선택합니다.

8. **삭제**를 선택하여 CNAME 레코드를 삭제합니다.


## <a name="clean-up-resources"></a>리소스 정리

위의 단계에서 사용자 지정 도메인을 CDN 엔드포인트에 추가했습니다. 더 이상 사용자 지정 도메인과 엔드포인트를 연결하지 않으려면 다음과 같은 단계를 수행하여 사용자 지정 도메인을 제거할 수 있습니다.
 
1. CDN 프로필에서 제거하려는 사용자 지정 도메인을 포함하는 엔드포인트를 선택합니다.

2. **엔드포인트** 페이지의 사용자 지정 도메인에서 제거하려는 사용자 지정 도메인을 마우스 오른쪽 단추로 클릭한 다음, 팝업 메뉴에서 **삭제**를 선택합니다.  

   사용자 지정 도메인은 엔드포인트에서 분리됩니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - CNAME DNS 레코드 만들기.
> - CDN 엔드포인트와 사용자 지정 도메인 연결.
> - 사용자 지정 도메인 확인.

Azure CDN 사용자 지정 도메인에서 HTTPS를 구성하는 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [자습서: Azure CDN 사용자 지정 도메인에서 HTTPS 구성](cdn-custom-ssl.md)


