---
title: Azure CDN 엔드포인트 제거 | Microsoft Docs
description: Azure Content Delivery Network 끝점에서 캐시 된 콘텐츠를 모두 제거 하는 방법을 알아봅니다. 에 지 노드는 ttl (time-to-live)이 만료 될 때까지 자산을 캐시 합니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: cc09c35ba5499c6e911ebd7dd23482ef30f931da
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192540"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Azure CDN 엔드포인트 제거
## <a name="overview"></a>개요
Azure CDN 가장자리 노드는 자산의 TTL(Time-to-Live)이 만료될 때 자산을 캐시합니다.  자산의 TTL이 만료된 후에 클라이언트가 가장자리 노드에서 자산을 요청하는 경우 가장자리 노드는 새로 업데이트된 자산 사본을 검색하여 클라이언트 요청을 처리하고 저장소는 캐시를 새로 고칩니다.

사용자가 항상 자산의 최신 복사본을 받도록 보장하는 가장 좋은 방법은 각 업데이트에 대해 자산 버전 관리를 수행하여 자산을 새 URL로 게시하는 것입니다.  CDN은 다음 클라이언트 요청에 대한 새 자산을 즉시 검색합니다.  경우에 따라 모든 가장자리 노드에서 캐시된 콘텐츠를 제거하고 이러한 콘텐츠가 모두 새로 업데이트된 자산을 검색하도록 강제하려 할 수 있습니다.  웹 애플리케이션에 대한 업데이트 또는 잘못된 정보가 포함된 신속한 업데이트 자산 때문일 수 있습니다.

> [!TIP]
> 제거는 CDN 에지 서버에 캐시된 콘텐츠만 지웁니다.  프록시 서버와 로컬 브라우저 캐시 같은 다운스트림 캐시에는, 캐시된 파일 사본이 유지될 수 있습니다.  파일의 TTL(Time-To-Live)을 설정할 때 이것을 기억하는 것이 중요합니다.  업데이트할 때마다 고유한 이름을 부여하거나 [쿼리 문자열 캐싱](cdn-query-string.md)의 이점을 활용하여, 다운스트림 클라이언트가 파일의 최신 버전을 요청하도록 할 수 있습니다.  
> 
> 

이 자습서는 엔드포인트의 모든 가장자리 노드에서 자산을 제거하는 과정을 안내합니다.

## <a name="walkthrough"></a>연습
1. [Azure Portal](https://portal.azure.com)에서 제거하려는 엔드포인트를 포함하는 CDN 프로필로 이동합니다.
2. CDN 프로필 블레이드에서 제거 단추를 클릭합니다.
   
    ![CDN 프로필 블레이드](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    제거 블레이드가 열립니다.
   
    ![CDN 제거 블레이드](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. 제거 블레이드에서 URL 드롭다운 목록에서 제거하려는 서비스 주소를 선택합니다.
   
    ![양식 제거](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > 또한 CDN 엔드포인트 블레이드에서 **제거** 단추를 클릭하여 제거 블레이드로 가져올 수 있습니다.  이 경우에 **URL** 필드는 해당 특정 엔드포인트의 서비스 주소로 미리 채워질 수 있습니다.
   > 
   > 
4. 가장자리 노드에서 제거하려는 자산을 선택합니다.  모든 자산을 지우려면 **모두 제거** 확인란을 클릭 합니다.  그렇지 않은 경우 제거하려는 각 자산의 경로를 **경로** 텍스트 상자에 입력합니다. 경로에 지원되는 형식은 아래와 같습니다.
    1. **단일 URL 제거**: 파일 확장명을 사용 하거나 사용 하지 않고 전체 URL을 지정 하 여 개별 자산을 제거 합니다 (예 `/pictures/strasbourg.png` :,). `/pictures/strasbourg`
    2. **와일드 카드 제거**: 별표(\*)를 와일드 카드로 사용할 수 있습니다. 경로에 `/*`가 포함된 엔드포인트의 모든 폴더, 하위 폴더 및 파일을 제거하거나 폴더를 지정하고 맨 뒤에 `/*`를 붙여(예: `/pictures/*`) 특정 폴더의 모든 하위 폴더 및 파일을 제거합니다.  와일드 카드 제거는 현재 Akamai의 Azure CDN에서 지원되지 않습니다. 
    3. **루트 도메인 제거**: 경로에 "/"가 포함된 엔드포인트의 루트를 제거합니다.
   
   > [!TIP]
   > 제거할 경로를 지정해야 하며 경로는 다음 [정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)에 맞는 상대 URL이어야 합니다. **모두 제거** 및 **와일드 카드 제거**는 현재 **Akamai의 Azure CDN**에서 지원되지 않습니다.
   > > 단일 URL 제거 `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > 쿼리 문자열 `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > 와일드 카드 제거 `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` 
   > 
   > 여러 자산 목록을 작성할 수 있도록 하는 텍스트를 입력한 후에 더 많은 **경로** 텍스트 상자가 표시됩니다.  목록에서 줄임표(...) 단추를 클릭하여 자산을 삭제할 수 있습니다.
   > 
5. **제거** 단추를 클릭합니다.
   
    ![제거 단추](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> 제거 요청은 **Microsoft에서 Azure CDN**를 처리 하는 데 약 10 분, Verizon (표준 및 프리미엄) **의 Azure CDN** , **Akamai의 Azure CDN**를 사용 하 여 약 10 초까지 소요 됩니다.  Azure CDN은 프로필 수준에서 동시 제거 요청이 항상 50개로 제한됩니다. 
> 
> 

## <a name="see-also"></a>참조
* [Azure CDN 엔드포인트에 자산 미리 로드](cdn-preload-endpoint.md)
* [Azure CDN REST API 참조 - 엔드포인트 제거 또는 미리 로드](/rest/api/cdn/endpoints)

