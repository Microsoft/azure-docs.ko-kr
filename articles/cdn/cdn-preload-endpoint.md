---
title: Azure CDN 엔드포인트에 자산 미리 로드 | Microsoft Docs
description: Azure CDN 엔드포인트에 캐시된 콘텐츠를 미리 로드하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: 0d34985c8d83e8adad43aeec36ead939d8b22132
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627425"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Azure CDN 엔드포인트에 자산 미리 로드
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

기본적으로 자산은 요청하는 경우에만 캐시됩니다. 가장자리 서버에 콘텐츠가 아직 캐시되지 않았고 원본 서버에 요청을 전달해야 하기 때문에 각 지역의 첫 번째 요청은 이후의 요청보다 더 오래 걸릴 수 있습니다. 이러한 첫 번째 지연을 방지하려면 자산을 미리 로드합니다. 더 나은 고객 경험을 제공할 뿐 아니라 캐시된 자산을 미리 로드하면 원본 서버에서 네트워크 트래픽을 줄일 수 있습니다.

> [!NOTE]
> 자산을 미리 로드하는 것은 대규모 이벤트 또는 새로운 동영상 릴리스나 소프트웨어 업데이트와 같이 많은 사용자가 동시에 사용할 수 있는 콘텐츠에 유용합니다.
> 
> 

이 자습서는 모든 Azure CDN 에지 노드에 캐시된 콘텐츠 미리 로드에 대해 안내합니다.

## <a name="to-pre-load-assets"></a>자산을 미리 로드하려면
1. [Azure Portal](https://portal.azure.com)에서 미리 로드하려는 엔드포인트를 포함하는 CDN 프로필로 이동합니다. 프로필 창이 열립니다.
    
2. 목록에서 엔드포인트를 클릭합니다. 엔드포인트 창이 열립니다.
3. CDN 엔드포인트 창에서 **로드**를 선택합니다.
   
    ![CDN 엔드포인트 창](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **로드** 창이 열립니다.
   
    ![CDN 로드 창](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. **콘텐츠 경로**에 로드하려는 각 자산의 전체 경로를 입력합니다(예: `/pictures/kitten.png`).
   
   > [!TIP]
   > 텍스트를 입력한 후에는 여러 자산 목록을 빌드할 수 있도록 더 많은 **콘텐츠 경로** 텍스트 상자가 나타납니다. 목록에서 자산을 삭제하려면 줄임표(...) 버튼을 선택하고 **삭제**를 선택합니다.
   > 
   > 각 콘텐츠 경로는 다음 [정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)에 맞는 상대 URL이어야 합니다.  
   > - 단일 파일 경로 로드: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - 쿼리 문자열을 사용하여 단일 파일 로드: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > 각 자산은 고유한 경로가 있기 때문에 자산을 미리 로드하기 위한 와일드카드 기능이 없습니다.
   > 
   > 
   
    ![로드 단추](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. 콘텐츠 경로를 입력했으면 **로드**를 선택합니다.
   

> [!NOTE]
> CDN 프로필별로 분당 로드 요청이 10개로 제한되며 한 번에 50개의 동시 경로를 처리할 수 있습니다. 각 경로의 길이는 1024자로 제한됩니다.
> 
> 

## <a name="see-also"></a>참고 항목
* [Azure CDN 엔드포인트 제거](cdn-purge-endpoint.md)
* [Azure CDN REST API 참조: 끝점에서 콘텐츠 미리 로드](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API 참조: 끝점에서 콘텐츠 제거](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

