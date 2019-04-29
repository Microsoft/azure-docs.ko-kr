---
title: 웹 페이지에서 Azure Traffic Manager의 실제 사용자 측정 | Microsoft Docs
description: Traffic Manager로 실제 사용자 측정을 보내도록 웹 페이지 설정
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: d6b669f5baae13f9fb57c5c58060e70a8f871e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884107"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>웹 페이지를 사용하여 Azure Traffic Manager의 실제 사용자 측정을 보내는 방법

RUM(실제 사용자 측정) 키를 가져오고 웹 페이지에 생성된 코드를 포함하여 실제 사용자 측정을 Traffic Manager로 보내도록 웹 페이지를 구성할 수 있습니다.

## <a name="obtain-a-real-user-measurements-key"></a>실제 사용자 측정 키 가져오기

클라이언트 애플리케이션에서 Traffic Manager로 보낸 측정값은 **RUM(실제 사용자 측정) 키**라고 하는 고유한 문자열을 사용하여 서비스에 의해 식별됩니다. Azure Portal, REST API를 사용하거나 PowerShell 또는 Azure CLI를 사용하여 RUM 키를 가져올 수 있습니다.

Azure Portal을 사용하여 RUM 키를 가져오려면:
1. 브라우저에서 Azure Portal에 로그인합니다. 아직 계정이 없는 경우 1개월 평가판에 등록할 수 있습니다.
2. 포털의 검색 창에서 수정하려는 Traffic Manager 프로필 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. Traffic Manager 프로필 블레이드에서 **설정** 아래의 **실제 사용자 측정**을 클릭합니다.
4. **키 생성**을 클릭하여 새 RUM 키를 만듭니다.
 
   ![실제 사용자 측정 키 생성](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **그림 1: 실제 사용자 측정 키 생성**

5. 블레이드에는 생성된 RUM 키와 HTML 페이지에 포함되어야 하는 JavaScript 코드 조각이 표시됩니다.
 
    ![실제 사용자 측정 키에 대한 Javascript 코드](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **그림 2: 실제 사용자 측정 키 및 측정 JavaScript**
 
6. **복사** 단추를 클릭하여 JavaScript 코드를 복사합니다. 

>[!IMPORTANT]
> 실제 사용자 측정 기능에 생성된 JavaScript를 사용하여 제대로 작동시킵니다. 이 스크립트 또는 실제 사용자 측정에서 사용한 스크립트에 대한 변경으로 인해 예기치 않은 동작이 발생할 수 있습니다.

## <a name="embed-the-code-to-an-html-web-page"></a>HTML 웹 페이지에 대한 코드 포함

RUM 키를 가져온 후에 다음 단계는 복사한 JavaScript를 최종 사용자가 방문하는 HTML 페이지에 포함하는 것입니다. HTML 편집 작업을 수행하려면 여러 가지 방법으로 다른 도구 및 워크플로를 사용합니다. 이 예제에서는 HTML 페이지를 업데이트하여 이 스크립트를 추가하는 방법을 보여줍니다. 이 지침을 사용하여 HTML 원본 관리 워크플로에 적용할 수 있습니다.

1.  텍스트 편집기에서 HTML 페이지를 엽니다.
2.  HTML의 본문 섹션에 이전 단계에서 복사한 JavaScript 코드를 붙여 넣습니다(복사한 코드는 8 및 9줄에 있음. 그림 3 참조).
 
    ![실제 사용자 측정을 위해 웹 페이지에 Javascript 코드 포함](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **그림 3: 실제 사용자 측정 JavaScript가 포함된 간단한 HTML**

3.  HTML 파일을 저장하고 인터넷에 연결된 웹 서버에 호스트합니다. 
4. 다음에 이 페이지가 웹 브라우저에서 렌더링될 때 참조된 JavaScript가 다운로드되고 스크립트가 측정 및 보고 작업을 실행합니다.


## <a name="next-steps"></a>다음 단계
- [실제 사용자 측정](traffic-manager-rum-overview.md)에 대한 자세한 정보
- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [Traffic Manager 프로필을 만드는](traffic-manager-create-profile.md)

