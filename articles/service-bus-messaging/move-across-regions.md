---
title: Service Bus 네임 스페이스를 다른 영역으로 이동 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 네임 스페이스를 현재 지역에서 다른 지역으로 이동 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861064"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Azure Service Bus 네임 스페이스를 다른 영역으로 이동
기존 Service Bus 네임 스페이스를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트를 위한 동일한 구성으로 네임 스페이스를 만들 수 있습니다. [재해 복구 계획](service-bus-geo-dr.md)의 일부로 다른 지역에 보조 네임 스페이스를 만들 수도 있습니다.

대략적인 단계는 다음과 같습니다.

1. 현재 지역의 Service Bus 네임 스페이스를 Azure Resource Manager 템플릿으로 내보냅니다. 
1. 템플릿에서 리소스의 위치를 업데이트 합니다. 또한 기본 규칙을 자동으로 만들 때 기본 규칙을 만들 수 없으므로 템플릿에서 기본 구독 필터를 제거 합니다. 
1. 템플릿을 사용 하 여 대상 지역에 Service Bus 네임 스페이스를 배포 합니다. 
1. 항목에 대 한 네임 스페이스, 큐, 토픽 및 구독이 모두 대상 지역에 만들어졌는지 확인 하려면 배포를 확인 합니다. 
1. 모든 메시지를 처리 한 후 소스 영역에서 네임 스페이스를 삭제 하 여 이동 작업을 완료 합니다. 

## <a name="prerequisites"></a>사전 준비 사항
계정에서 사용 하는 Azure Service Bus 및 기능이 대상 지역에서 지원 되는지 확인 합니다.
 
## <a name="prepare"></a>준비
시작 하려면 리소스 관리자 템플릿을 내보냅니다. 이 템플릿에는 Service Bus 네임 스페이스를 설명 하는 설정이 포함 되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 리소스** 를 선택한 다음 Service Bus 네임 스페이스를 선택 합니다.
3. > **설정**  >  **템플릿 내보내기**를 선택 합니다.
4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택 합니다.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="리소스 관리자 템플릿 다운로드":::
5. 포털에서 다운로드 한 .zip 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다. 이 zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함 되어 있습니다. 
1. 추출 된 폴더의 파일에 template.js를 엽니다. 
1. 를 검색 하 `location` 고 속성 값을 지역 또는 위치의 새 이름으로 바꿉니다. 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백을 사용 하지 않는 지역 이름입니다 (예: `West US` ) `westus` .
1. 다음 형식의 리소스에 대 한 정의를 제거 `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` 합니다. `,`JSON을 유효 하 게 유지 하려면이 섹션 앞에 오는 쉼표 () 문자를 제거 해야 합니다.  

    > [!NOTE]
    > 리소스 관리자 템플릿을 사용 하 여 구독에 대 한 기본 규칙을 만들 수 없습니다. 기본 규칙은 대상 지역에서 구독을 만들 때 자동으로 생성 됩니다. 

## <a name="move"></a>이동
템플릿을 배포 하 여 대상 지역에 Service Bus 네임 스페이스를 만듭니다. 

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
2. **Marketplace 검색**에서 검색 텍스트에 대 한 **템플릿 배포** 를 입력 하 고 **템플릿 배포 (사용자 지정 템플릿을 사용 하 여 배포)** 를 선택한 다음 **enter**키를 누릅니다.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="새 템플릿 배포":::    
1. **템플릿 배포** 페이지에서 **만들기**를 선택 합니다.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="새 템플릿 배포-만들기 단추":::        
1. **사용자 지정 배포** 페이지에서 **편집기에서 사용자 고유의 템플릿 빌드**를 선택 합니다.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="편집기에서 사용자 고유의 템플릿 빌드-링크":::            
1. **템플릿 편집** 페이지의 도구 모음에서 **파일 로드** 를 선택 하 고 지침에 따라 마지막 섹션에서 다운로드 한 파일 **에template.js** 를 로드 합니다.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="템플릿 선택":::                
1. **저장** 을 선택 하 여 템플릿을 저장 합니다. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="템플릿 저장":::                    
1. **사용자 지정 배포** 페이지에서 다음 단계를 수행 합니다. 
    1. Azure **구독**을 선택 합니다. 
    2. 기존 **리소스 그룹**을 선택하거나 리소스 그룹을 만듭니다. 
    3. 대상 **위치** 또는 지역을 선택 합니다. 기존 리소스 그룹을 선택한 경우이 설정은 읽기 전용입니다. 
    4. **네임 스페이스의 새 이름을**입력 합니다.
    1. **검토 + 만들기**를 선택합니다. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="리소스 관리자 템플릿 배포":::
    1. **검토 + 만들기** 페이지의 페이지 맨 아래에서 **만들기** 를 선택 합니다. 
    
## <a name="verify"></a>확인
1. 배포가 성공적으로 완료 되 면 **리소스 그룹으로 이동**을 선택 합니다.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="리소스 그룹으로 이동 링크":::    
1. **리소스 그룹** 페이지에서 Service Bus 네임 스페이스를 선택 합니다. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Service Bus 네임 스페이스 선택":::    
1. **Service Bus 네임 스페이스** 페이지에서 원본 지역의 큐, 토픽 및 구독이 표시 되는지 확인 합니다. 
    1. 오른쪽 창 맨 아래에 있는 네임 스페이스에 **큐** 가 표시 됩니다.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="네임 스페이스의 큐":::
    2. **항목** 탭으로 전환 하 여 네임 스페이스의 항목을 확인 합니다.
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="네임 스페이스의 항목":::
    3. 구독을 만들었는지 확인 하려면 항목을 선택 합니다. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="토픽 구독":::      
    
    

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후에는 시작 하려는 경우 **대상 Service Bus 네임 스페이스**를 삭제 하 고이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명 된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 Service Bus 네임 스페이스의 이동을 완료 하려면 **소스 Service Bus 네임 스페이스**를 삭제 합니다. 네임 스페이스를 삭제 하기 전에 모든 메시지를 처리 하는지 확인 합니다. 

Azure Portal를 사용 하 여 Service Bus 네임 스페이스 (원본 또는 대상)를 삭제 하려면 다음을 수행 합니다.

1. Azure Portal의 맨 위에 있는 검색 창에서 **Service Bus**를 입력 하 고 검색 결과에서 **Service Bus** 을 선택 합니다. 목록에 Service Bus 네임 스페이스가 표시 됩니다.
2. 삭제할 대상 네임 스페이스를 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 

    ![네임 스페이스 삭제-단추](./media/move-across-regions/delete-namespace-button.png)
3. **리소스 삭제** 페이지에서 선택한 리소스를 확인 하 고 **예**를 입력 하 여 삭제를 확인 한 다음 **삭제**를 선택 합니다. 

    다른 옵션은 Service Bus 네임 스페이스가 있는 리소스 그룹을 삭제 하는 것입니다. **리소스 그룹** 페이지의 도구 모음에서 **리소스 그룹 삭제** 를 선택 하 고 삭제를 확인 합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Service Bus 네임 스페이스를 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
