---
title: Azure Event Grid 시스템 항목을 다른 영역으로 이동
description: 이 문서에서는 한 지역에서 다른 지역으로 Azure Event Grid 시스템 항목을 이동 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086184"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Azure Event Grid 시스템 항목을 다른 영역으로 이동
여러 가지 이유로 리소스를 다른 지역으로 이동 하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용 하 여 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나 용량 계획 요구 사항에 대 한 응답으로 사용할 수 있습니다. 

이 문서에서 다루는 개략적인 단계는 다음과 같습니다. 

- Azure Storage 계정 및 연결 된 시스템 항목을 포함 하는 **리소스 그룹** 을 리소스 관리자 템플릿으로 내보냅니다. 시스템 토픽의 템플릿만 내보낼 수도 있습니다. 이 경로로 이동 하는 경우 시스템 항목을 이동 하기 전에 Azure 이벤트 원본 (이 예제에서는 Azure Storage 계정)을 다른 지역으로 이동 해야 합니다. 그런 다음 시스템 항목에 대해 내보낸 템플릿에서 대상 지역의 저장소 계정에 대 한 외부 ID를 업데이트 합니다. 
- **템플릿을 수정** 하 여 `endpointUrl` 시스템 토픽을 구독 하는 webhook를 가리키도록 속성을 추가 합니다. 시스템 항목을 내보낼 때 해당 구독 (이 경우 webhook)도 템플릿으로 내보내지고 `endpointUrl` 속성은 포함 되지 않습니다. 따라서 항목을 구독 하는 끝점을 가리키도록 업데이트 해야 합니다. 또한 속성의 값을 `location` 새 위치 또는 지역으로 업데이트 합니다. 다른 형식의 이벤트 처리기의 경우 위치만 업데이트 해야 합니다. 
- **템플릿을 사용 하** 여 대상 지역에 리소스를 배포 합니다. 저장소 계정의 이름과 대상 지역에 만들 시스템 항목을 지정 합니다. 
- **배포 확인**. 대상 지역의 blob 저장소에 파일을 업로드할 때 webhook가 호출 되는지 확인 합니다. 
- **이동을 완료**하려면 원본 영역에서 리소스 (이벤트 원본 및 시스템 항목)를 삭제 합니다. 

## <a name="prerequisites"></a>필수 조건
- 빠른 시작: 소스 지역에서 Azure Portal를 사용 하 여 [웹 끝점으로 Blob storage 이벤트 라우팅을](blob-event-quickstart-portal.md) 완료 합니다. 이 단계는 **선택 사항**입니다. 이 문서의 단계를 테스트 합니다. App Service 및 App Service 계획의 별도 리소스 그룹에 저장소 계정을 유지 합니다. 
- 대상 지역에서 Event Grid 서비스를 사용할 수 있는지 확인 합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)을 참조하세요.

## <a name="prepare"></a>준비
시작 하려면 시스템 이벤트 원본 (Azure Storage 계정) 및 관련 시스템 항목이 포함 된 리소스 그룹에 대 한 리소스 관리자 템플릿을 내보냅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택 합니다. 그런 다음 시스템 항목을 만든 이벤트 원본이 포함 된 리소스 그룹을 선택 합니다. 다음 예제에서는 **Azure Storage** 계정입니다. 리소스 그룹에는 저장소 계정 및 연결 된 시스템 항목이 포함 되어 있습니다. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="리소스 그룹 페이지":::        
3. 왼쪽 메뉴의 **설정**에서 **템플릿 내보내기** 를 선택한 다음 도구 모음에서 **다운로드** 를 선택 합니다. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="저장소 바운드 계정-템플릿 내보내기 페이지":::        
5. 포털에서 다운로드 한 **.zip** 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다. 이 zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함 되어 있습니다. 
1. 선택한 편집기에서 **template.js** 를 엽니다. 
1. Webhook에 대 한 URL을 템플릿으로 내보내지 않습니다. 따라서 다음 단계를 수행 합니다.
    1. 템플릿 파일에서 **WebHook**를 검색 합니다. 
    1. **속성** 섹션에서 `,` 마지막 줄의 끝에 쉼표 () 문자를 추가 합니다. 이 예에서는 `"preferredBatchSizeInKilobytes": 64`입니다. 
    1. `endpointUrl`다음 예제와 같이 WEBHOOK URL에 설정 된 값을 사용 하 여 속성을 추가 합니다. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > 다른 형식의 이벤트 처리기의 경우 모든 속성을 템플릿으로 내보냅니다. `location`다음 단계에 표시 된 것 처럼 대상 영역에 대 한 속성을 업데이트 하기만 하면 됩니다. 
7. `location` **저장소 계정** 리소스를 대상 지역 또는 위치로 업데이트 합니다. 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백을 사용 하지 않는 지역 이름입니다 (예: `West US` ) `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. `location`템플릿의 **system 토픽** 리소스에 대해 업데이트 하는 단계를 반복 합니다. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. 템플릿을 **저장** 합니다. 

## <a name="recreate"></a>다시 
템플릿을 배포 하 여 대상 지역의 저장소 계정에 대 한 저장소 계정 및 시스템 항목을 만듭니다. 

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
    1. **시스템 항목 이름**에 저장소 계정과 연결 될 시스템 항목의 이름을 입력 합니다.  
    1. **저장소 계정 이름**에는 대상 지역에 만들 저장소 계정의 이름을 입력 합니다. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="리소스 관리자 템플릿 배포":::
    5. 페이지 아래쪽에서 **검토 + 만들기**를 선택합니다. 
    1. **검토 + 만들기** 페이지에서 설정을 검토 하 고 **만들기**를 선택 합니다. 

## <a name="verify"></a>확인
1. 배포가 성공 하면 **이동 리소스 그룹**을 선택 합니다. 
1. **리소스 그룹** 페이지에서 이벤트 원본 (이 예제에서는 Azure Storage 계정) 및 시스템 항목이 만들어졌는지 확인 합니다. 
1. Azure Blob storage의 컨테이너에 파일을 업로드 하 고 webhook가 이벤트를 받았는지 확인 합니다. 자세한 내용은 [끝점에 이벤트 보내기](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)를 참조 하세요.

## <a name="discard-or-clean-up"></a>삭제 또는 정리
이동을 완료 하려면 저장소 계정 및 연결 된 시스템 항목이 포함 된 리소스 그룹을 원본 지역에 삭제 합니다.  

다시 시작 하려면 대상 지역에서 리소스 그룹을 삭제 하 고이 문서의 [준비](#prepare) 및 [다시 만들기](#recreate) 섹션에서 단계를 반복 합니다.

Azure Portal를 사용 하 여 리소스 그룹 (원본 또는 대상)을 삭제 하려면 다음을 수행 합니다.

1. Azure Portal의 맨 위에 있는 검색 창에서 **리소스 그룹**을 입력 하 고 검색 결과에서 **리소스 그룹** 을 선택 합니다. 
2. 삭제할 리소스 그룹을 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="리소스 그룹 삭제":::
3. 확인 페이지에서 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.  

## <a name="next-steps"></a>다음 단계
한 지역에서 다른 지역으로 Azure 이벤트 원본 및 연결 된 시스템 항목을 이동 하는 방법을 배웠습니다. 사용자 지정 토픽, 도메인 및 파트너 네임 스페이스를 지역 간에 이동 하려면 다음 문서를 참조 하세요.

- [지역 간에 사용자 지정 항목을 이동](move-custom-topics-across-regions.md)합니다. 
- [도메인을 지역 간에 이동](move-domains-across-regions.md)합니다. 
- [여러 지역에서 파트너 네임 스페이스를 이동](move-partner-namespaces-across-regions.md)합니다. 

Azure에서 지역과 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)문서를 참조 하세요.
