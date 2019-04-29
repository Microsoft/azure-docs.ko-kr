---
title: Azure 애플리케이션 검토 피드백 처리 - Azure Marketplace | Microsoft Docs
description: Azure DevOps를 사용하여 Azure Marketplace용 Azure 애플리케이션 제품에 대한 검토 피드백을 처리하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: e5fe076cfe733b4fa0151570603c090af98de0e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745586"
---
# <a name="handling-review-feedback"></a>검토 피드백 처리

이 문서에서는 Microsoft Azure Marketplace 검토 팀이 사용하는 Azure DevOps 환경에 액세스하는 방법을 설명합니다.  **Microsoft 검토** 단계 중에 Azure 애플리케이션 제품에서 심각한 문제가 발견되면 이 시스템에 로그인하여 해당 문제에 대한 자세한 정보(검토 피드백)를 볼 수 있습니다.  이러한 문제를 모두 해결한 후 Azure Marketplace에서 계속 게시하려면 제품을 다시 제출해야 합니다.  다음 다이어그램은 이 피드백 프로세스와 게시 프로세스 간의 관계를 보여 줍니다.

![VSTS 피드백이 있는 게시 단계](./media/pub-flow-vsts-access.png)

일반적으로 검토 문제는 PR(끌어오기 요청)로 참조됩니다.  문제에 대한 세부 정보를 포함하는 온라인 [Azure DevOps](https://azure.microsoft.com/services/devops/)(이전의 VSTS(Visual Studio Team Services)) 항목에 각 PR이 연결됩니다.  다음 이미지에는 검토 PR 참조 예가 나와 있습니다.  복잡한 상황의 경우 검토 및 지원 팀이 메일을 보낼 수도 있습니다. 

![검토 피드백이 표시된 상태 탭](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>VSTS 액세스

검토 피드백에서 참조된 PR 항목을 보려면 먼저 게시자에게 적절한 권한이 부여되어야 합니다.  그러지 않으면 새 게시자가 PR를 보려고 할 때 `401 - Not Authorized` 응답 페이지가 표시됩니다.  Azure DevOps 리포지토리에 대한 액세스 권한을 요청하려면 다음 단계를 수행합니다.

1. 다음 정보를 수집합니다.
    - 게시자 이름 및 ID
    - 제품 유형(Azure 앱), 제품 이름 및 SKU ID
    - 끌어오기 요청 링크(예: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`) 이 URL은 알림 메시지 또는 401 응답 페이지의 주소에서 검색할 수 있습니다.
    - 액세스 권한을 부여할 게시 조직의 개인 메일 주소.  이 목록에는 Cloud 파트너 포털에서 게시자로 등록할 때 제공한 소유자 주소가 포함되어야 합니다.
2. 지원 인시던트를 만듭니다.  Cloud 파트너 포털의 제목 표시줄에서 **도움말** 단추를 선택한 다음, 메뉴에서 **지원**을 선택합니다.  기본 웹 브라우저가 시작되고 Microsoft 새 지원 인시던트 페이지로 이동합니다.  먼저 로그인해야 할 수도 있습니다.
3. **문제 유형**을 **마켓플레이스 온보딩**으로, **범주**를 **액세스 문제**로 지정한 다음, **요청 시작**을 선택합니다.

    ![지원 티켓 범주](./media/support-incident1.png)

4. **1/2단계** 페이지에서 연락처 정보를 제공하고 **계속**을 선택합니다.
5. **2/2단계** 페이지에서 인시던트 제목(예: `Request VSTS access`)을 지정하고 첫 번째 단계(위)에서 수집한 정보를 제공합니다.  계약을 읽고 동의한 다음, **제출**을 선택합니다.

인시던트 만들기에 성공하면 확인 페이지가 표시됩니다.  참조할 수 있도록 이 페이지의 확인 정보를 저장합니다.  Microsoft 지원 팀은 업무일 기준으로 며칠 이내에 액세스 요청에 응답합니다.


## <a name="reviewing-the-pull-request"></a>끌어오기 요청 검토 

끌어오기 요청에 문서화된 문제를 검토하려면 다음 절차를 따릅니다.

1. **게시 단계** 양식의 **Microsoft 검토** 섹션에서 PR 링크를 클릭하여 브라우저를 시작하고 이 PR에 대한 **개요**(홈) 페이지로 이동합니다.  다음 이미지는 Contoso 샘플 앱 제품의 심각한 문제 홈페이지 예를 보여 줍니다.  이 페이지에는 Azure 앱에서 발견된 검토 문제에 대한 유용한 요약 정보가 포함되어 있습니다.  

    [![끌어오기 요청 홈 페이지](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> 펼치려면 이미지를 클릭하세요.
    
2. (선택 사항) 창의 오른쪽에 있는 **정책** 섹션에서 문제 메시지(이 예제에서는 **정책 유효성 검사 실패**)를 클릭하여 연관된 로그 파일을 비롯한 문제의 하위 수준 세부 정보를 조사합니다.  일반적으로 오류는 로그 파일의 맨 아래에 표시됩니다.

3. 홈페이지의 왼쪽 메뉴에서 **파일**을 선택하여 이 제품의 기술 자산을 구성하는 목록 파일을 표시합니다.  Microsoft 검토자가 발견된 심각한 문제를 설명하는 주석을 추가해 두었을 것입니다.  다음 예제에서는 두 가지 문제가 발견되었습니다. 

    [![끌어오기 요청 홈 페이지](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> 펼치려면 이미지를 클릭하세요.

4. 왼쪽 트리에서 각 주석 노드를 클릭하여 주변 코드 컨텍스트의 주석으로 이동합니다.  팀 프로젝트의 소스 코드를 수정하여 주석에서 설명된 문제를 해결합니다.

> [!Note]
> 검토 팀의 Azure DevOps 환경에서는 제품의 기술 자산을 편집할 수 없습니다.  게시자에게는 포함된 소스 코드에 대한 읽기 전용 환경입니다.  그러나 Microsoft 검토 팀을 위해 주석에 회신을 남길 수 있습니다.

   다음 예제에서는 게시자가 첫 번째 문제를 검토, 수정 및 회신했습니다.

   ![첫 번째 수정 및 주석 회신](./media/first-comment-reply.png)


## <a name="next-steps"></a>다음 단계

검토 PR에 문서화된 심각한 문제를 해결한 후에는 [Azure 앱 제품을 다시 게시](./cpp-publish-offer.md)해야 합니다.
