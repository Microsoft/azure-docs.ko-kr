---
title: Azure Security Center의 보안 플레이북 | Microsoft Docs
description: 이 문서를 통해 Azure Security Center에서 보안 플레이북을 사용하여 보안 인시던트에 대한 사용자 응답을 자동화할 수 있습니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ec16e6daec099adbede625c5ec6fe6909059143b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60907095"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Azure Security Center의 보안 플레이북(미리 보기)
이 문서를 통해 Azure Security Center에서 보안 플레이북을 사용하여 보안 관련 문제에 응답할 수 있습니다.

## <a name="what-is-security-playbook-in-security-center"></a>Security Center의 보안 플레이북이란?
보안 플레이북은 특정 플레이북이 선택한 경고에서 트리거되면 Security Center에서 실행될 수 있는 프로시저의 컬렉션입니다. 보안 플레이북은 Security Center에서 감지한 특정 보안 경고에 대한 사용자 응답을 자동화하고 오케스트레이션할 수 있습니다. Security Center의 보안 플레이북은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)에 기반합니다. 즉, Logic Apps 템플릿의 보안 범주 아래에서 제공되는 템플릿을 사용하거나, 요구 사항에 따라 수정하거나, [Azure Logic Apps 워크플로](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) 및 트리거한 Security Center를 사용하여 새로운 플레이북을 만들 수 있습니다.

> [!NOTE]
> 플레이북은 Azure Logic Apps를 활용하므로 요금이 청구됩니다. 자세한 내용은 [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) 가격 책정 페이지를 방문하세요.

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Security Center에서 보안 플레이 북을 만들려면 어떻게 하나요?
다음 단계를 따라 Security Center에서 보안 플레이 북을 만듭니다.

1.  **Security Center** 대시보드를 엽니다.
2.  왼쪽 창의 **자동화 및 오케스트레이션** 섹션 아래에서 **플레이북(미리 보기)** 을 클릭합니다.

    ![논리 앱](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. **Security Center - 플레이북(미리 보기)** 페이지에서 **추가** 단추를 클릭합니다.

    ![논리 앱 만들기](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. **논리 앱 만들기** 페이지에서 요청된 정보를 입력하여 새 논리 앱을 만들고 **만들기** 단추를 클릭합니다. 만들기가 완료되면 새로운 플레이북이 목록에 표시됩니다. 표시되지 않으면 **새로 고침** 단추를 클릭합니다. 일단 표시되면 클릭하여 이 플레이북의 편집을 시작합니다.

    ![논리 앱 만들기](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. **논리 앱 디자이너**가 나타납니다. **빈 논리 앱**을 클릭하여 새로운 플레이북을 만듭니다. 범주 아래에서 **보안**을 선택하고 템플릿 중 하나를 사용할 수도 있습니다.

    ![논리 앱 디자이너](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. **모든 커넥터 및 트리거 검색** 필드에서 *Azure Security Center*를 입력하고 **Azure Security Center 경고에 대한 응답이 트리거되는 경우**를 선택합니다.

    ![트리거](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. 이제 플레이북을 트리거하는 경우 상황을 정의할 수 있습니다. 작업, 논리 조건, 전환 사례 조건 또는 루프를 추가할 수 있습니다.

    ![논리 앱 디자이너](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Security Center의 보안 플레이북을 실행하는 방법

오케스트레이션하려 하거나, 다른 서비스에서 자세한 정보를 가져오거나 업데이트를 관리하려는 경우 Security Center에서 보안 플레이북을 실행할 수 있습니다. 플레이북에 액세스하려면 다음 단계를 따르세요.

1.  **Security Center** 대시보드를 엽니다.
2.  왼쪽 창의 **위협 감지** 아래에서 **보안 인시던트 및 경고**를 클릭합니다.

    ![경고](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  조사하려는 경고를 클릭합니다.
4.  경고 페이지 맨 위에서 **플레이북 실행** 단추를 클릭합니다.

    ![플레이북 실행](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. 플레이북 페이지에서 실행하려는 플레이북을 선택하고 **실행** 단추를 클릭합니다. 트리거하기 전에 플레이북을 확인하려는 경우 클릭하면 디자이너가 열리게 됩니다.

    ![플레이북](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>기록

플레이북을 실행한 후에 이전 실행 및 이전에 실행된 플레이북의 상태에 대한 자세한 정보를 포함하는 단계에 액세스할 수 있습니다. 기록은 경고의 문맥에 따릅니다. 즉, 이 페이지에 나타나는 플레이북 기록은 이 플레이북을 트리거한 경고와 상호 관련됩니다.

![기록](./media/security-center-playbooks/security-center-playbooks-fig16.png)

특정 플레이북의 실행에 대한 자세한 내용을 보려면 플레이북 자체를 클릭합니다. 그러면 전체 워크플로를 포함한 논리 앱 실행 페이지가 나타납니다.

![세부 정보](./media/security-center-playbooks/security-center-playbooks-fig14.png)

이 워크플로에서 각 작업이 실행하는 데 걸린 시간을 확인할 수 있습니다. 또한 각 작업을 확장하여 결과를 볼 수 있습니다.

### <a name="changing-an-existing-playbook"></a>기존 플레이북 변경

Security Center에서 기존 플레이북을 변경하여 작업이나 조건을 추가할 수 있습니다. 그렇게 하려면 변경하려는 플레이북의 이름을 클릭하고 플레이북 탭에서 논리 앱 디자이너를 열기만 하면 됩니다.

> [!NOTE]
> Azure Logic App을 사용하여 고유한 플레이북을 만드는 방법에 대한 자세한 내용은 [클라우드 앱과 클라우드 서비스 간에 프로세스를 자동화하는 첫 번째 논리 앱 워크플로 만들기](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app)를 참고하세요.


## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center에서 플레이북을 사용하는 방법을 살펴보았습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
