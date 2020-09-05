---
title: Azure Event Grid 도메인을 다른 지역으로 이동
description: 이 문서에서는 Azure Event Grid 도메인을 한 지역에서 다른 지역으로 이동 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086214"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Azure Event Grid 도메인을 다른 지역으로 이동
여러 가지 이유로 리소스를 다른 지역으로 이동 하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용 하 여 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나 용량 계획 요구 사항에 대 한 응답으로 사용할 수 있습니다. 

이 문서에서 다루는 개략적인 단계는 다음과 같습니다. 

- **도메인** 리소스를 Azure Resource Manager 템플릿으로 내보냅니다. 

    > [!IMPORTANT]
    > 도메인의 도메인 리소스와 항목을 템플릿으로 내보냅니다. 도메인에 대 한 구독 항목은 내보내지지 않습니다. 
- **템플릿을 사용 하** 여 대상 지역에 도메인을 배포 합니다. 
- 대상 지역에서 **수동으로 도메인 항목에 대 한 구독을 만듭니다** . 도메인을 현재 지역의 템플릿으로 내보내면 도메인 항목에 대 한 구독을 내보내지 않습니다. 따라서 도메인 및 도메인 항목이 대상 영역에 생성 된 후에 해당 항목을 만듭니다. 
- **배포 확인**. 도메인의 도메인 항목에 이벤트를 보내고 구독과 연결 된 이벤트 처리기가 호출 되었는지 확인 합니다. 
- **이동을 완료**하려면 원본 영역에서 도메인을 삭제 합니다. 

## <a name="prerequisites"></a>필수 조건
- 대상 지역에서 Event Grid 서비스를 사용할 수 있는지 확인 합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)을 참조하세요.

## <a name="prepare"></a>준비
시작 하려면 해당 도메인에 대 한 리소스 관리자 템플릿을 내보냅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 표시줄에 **Event Grid 도메인**을 입력 하 고 결과 목록에서 **Event Grid 도메인** 을 선택 합니다. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Event Grid 도메인 검색 및 선택":::
3. 리소스 관리자 템플릿으로 내보내려는 **도메인** 을 선택 합니다. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="도메인 선택":::   
4. **Event Grid 도메인** 페이지에서 왼쪽 메뉴의 **설정** 아래에서 **템플릿 내보내기** 를 선택한 다음 도구 모음에서 **다운로드** 를 선택 합니다. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="템플릿 내보내기-> 다운로드" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > 도메인 및 도메인 항목을 내보냅니다. 도메인 항목에 대 한 구독을 내보내지 않습니다. 따라서 도메인 항목을 이동한 후 도메인 항목에 대 한 구독을 만들어야 합니다. 
5. 포털에서 다운로드 한 **.zip** 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다. 이 zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함 되어 있습니다. 
1. 선택한 편집기에서 **template.js** 를 엽니다. 
8. `location`대상 지역 또는 위치로 **도메인** 리소스를 업데이트 합니다. 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백을 사용 하지 않는 지역 이름입니다 (예: `West US` ) `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. 템플릿을 **저장** 합니다. 

## <a name="recreate"></a>다시 
템플릿을 배포 하 여 대상 지역에 도메인 및 도메인 항목을 만듭니다. 

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
2. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.
3. **템플릿 배포**를 선택 합니다.
4. **만들기**를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드**를 선택 하 고 지침에 따라 마지막 섹션에서 다운로드 한 파일 **에template.js** 를 로드 합니다.
7. **저장** 을 선택 하 여 템플릿을 저장 합니다. 
8. **사용자 지정 배포** 페이지에서 다음 단계를 수행 합니다.
    1. Azure **구독**을 선택 합니다. 
    1. 대상 지역에서 기존 **리소스 그룹** 을 선택 하거나 하나를 만듭니다. 
    1. **지역**에서 대상 지역을 선택 합니다. 기존 리소스 그룹을 선택한 경우이 설정은 읽기 전용입니다. 
    1. **도메인 이름**에 도메인의 새 이름을 입력 합니다. 
    1. **검토 + 만들기**를 선택합니다. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="템플릿 배포":::        
    1. 템플릿의 유효성 검사가 성공 하면 페이지 맨 아래에서 **만들기** 를 선택 하 여 리소스를 배포 합니다. 
    1. 배포에 성공한 후 **리소스 그룹으로 이동** 을 선택 하 여 리소스 그룹 페이지로 이동 합니다. 리소스 그룹에 도메인이 있는지 확인 합니다. 도메인을 선택 합니다. 도메인에 도메인 항목이 있는지 확인 합니다. 

## <a name="discard-or-clean-up"></a>삭제 또는 정리
이동을 완료 하려면 원본 영역에서 도메인을 삭제 합니다.  

다시 시작 하려면 대상 지역에서 도메인을 삭제 하 고이 문서의 [준비](#prepare) 및 [다시 만들기](#recreate) 섹션에서 단계를 반복 합니다.

Azure Portal를 사용 하 여 도메인을 삭제 하려면 다음을 수행 합니다.

1. Azure Portal 맨 위에 있는 검색 창에서 **Event Grid 도메인**을 입력 하 고 검색 결과에서 **도메인 Event Grid** 을 선택 합니다. 
2. 삭제할 도메인을 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 
3. 확인 페이지에서 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.  

Azure Portal를 사용 하 여 도메인을 포함 하는 리소스 그룹을 삭제 하려면 다음을 수행 합니다.

1. Azure Portal의 맨 위에 있는 검색 창에서 **리소스 그룹**을 입력 하 고 검색 결과에서 **리소스 그룹** 을 선택 합니다. 
2. 삭제할 리소스 그룹을 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 
3. 확인 페이지에서 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.  

## <a name="next-steps"></a>다음 단계
Event Grid 도메인을 한 지역에서 다른 지역으로 이동 하는 방법을 배웠습니다. 지역에서 시스템 항목, 사용자 지정 항목 및 파트너 네임 스페이스를 이동 하는 방법에 대 한 다음 문서를 참조 하세요.

- [여러 지역에서 시스템 항목을 이동](move-system-topics-across-regions.md)합니다. 
- [지역 간에 사용자 지정 항목을 이동](move-custom-topics-across-regions.md)합니다. 
- [여러 지역에서 파트너 네임 스페이스를 이동](move-partner-namespaces-across-regions.md)합니다.

Azure에서 지역과 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)문서를 참조 하세요.