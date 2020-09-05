---
title: Azure Security Center의 적응형 애플리케이션 제어
description: 이 문서에서는 Azure Security Center에서 적응 응용 프로그램 제어를 사용 하 여 Azure 컴퓨터에서 실행 되는 응용 프로그램을 나열할 수 있습니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2020
ms.author: memildin
ms.openlocfilehash: d4ed88dea0d230fd3488621c217ef9ea086310d5
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691482"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>적응 응용 프로그램 컨트롤을 사용 하 여 컴퓨터의 공격 노출 영역 축소

Azure Security Center의 적응 응용 프로그램 컨트롤의 이점 및이 데이터 기반 지능형 기능으로 보안을 향상 시키는 방법에 대해 알아봅니다.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Security Center의 적응 응용 프로그램 컨트롤은 무엇 인가요?

적응 응용 프로그램 컨트롤은 컴퓨터에 대해 알려진 안전한 응용 프로그램의 허용 목록을 정의 하기 위한 인텔리전트 및 자동화 된 솔루션입니다. 

조직에는 종종 동일한 프로세스를 정기적으로 실행 하는 컴퓨터 컬렉션이 있습니다. Security Center는 기계 학습을 사용 하 여 컴퓨터에서 실행 되는 응용 프로그램을 분석 하 고 알려진 안전한 소프트웨어 목록을 만듭니다. 허용 목록은 특정 Azure 워크 로드를 기반으로 하며, 아래 지침을 사용 하 여 권장 사항을 추가로 사용자 지정할 수 있습니다.

적응 응용 프로그램 제어를 사용 하도록 설정 하 고 구성 하면 안전한 것으로 정의한 응용 프로그램이 아닌 다른 응용 프로그램을 실행 하는 경우 보안 경고를 받게 됩니다.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>적응 응용 프로그램 컨트롤의 이점은 무엇 인가요?

알려진 안전한 응용 프로그램의 목록을 정의 하 고 다른 항목이 실행 될 때 경고를 생성 하 여 여러 보안 강화 목표를 달성할 수 있습니다.

- 맬웨어 방지 솔루션에 의해 누락 될 수 있는 경우에도 잠재적 맬웨어를 식별 합니다.
- 사용이 허가 된 소프트웨어만 사용 하도록 규정 하는 로컬 보안 정책 준수 향상
- 이전 또는 지원 되지 않는 응용 프로그램을 실행 하지 않습니다.
- 조직에서 금지 하는 특정 소프트웨어 방지
- 중요 한 데이터에 액세스 하는 앱의 감독 증가



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|일반 공급|
|결정|표준 계층|
|지원 되는 컴퓨터:|![예, ](./media/icons/yes-icon.png) Windows 및 Linux를 실행 하는 azure 및 비 azure 컴퓨터<br>![예 ](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) 컴퓨터|
|필요한 역할 및 사용 권한:|**보안 판독기** 및 **읽기** 권한자 역할은 그룹 및 알려진 안전한 응용 프로그램 목록을 모두 볼 수 있습니다.<br>**참가자** 및 **보안 관리자** 역할은 그룹 및 알려진 안전한 응용 프로그램 목록을 모두 편집할 수 있습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>컴퓨터 그룹에서 응용 프로그램 제어 사용

구독에서 유사한 응용 프로그램 집합을 일관 되 게 실행 하는 컴퓨터 그룹을 식별 한 Security Center 경우, **컴퓨터에서 안전 응용 프로그램을 정의 하기 위한 적응 응용 프로그램 제어를 사용 하도록 설정**하 라는 메시지가 표시 됩니다.

권장 사항을 선택 하거나 적응 응용 프로그램 컨트롤 페이지를 열어 알려진 알려진 안전한 응용 프로그램 및 컴퓨터 그룹 목록을 봅니다.

1. Security Center의 메뉴에서 **적응 응용 프로그램 컨트롤**을 선택 합니다.

    다음 탭으로 그룹화 된 Vm이 있는 **적응 응용 프로그램 컨트롤** 페이지가 열립니다.

    - **구성** 됨-응용 프로그램의 허용 목록이 이미 정의 된 컴퓨터 그룹입니다. 구성 된 탭은 각 그룹에 대해 다음을 보여 줍니다.
        - 그룹에 있는 컴퓨터 수
        - 최근 경고

    - **권장** -동일한 응용 프로그램을 일관 되 게 실행 하 고 허용 목록이 구성 되지 않은 컴퓨터 그룹입니다. 이러한 그룹에 대해 적응 응용 프로그램 컨트롤을 사용 하는 것이 좋습니다.
    
      > [!TIP]
      > 접두사가 "REVIEWGROUP" 인 그룹 이름이 표시 되는 경우에는 부분적으로 일관 된 응용 프로그램 목록이 있는 컴퓨터가 포함 됩니다. 패턴을 볼 수 없지만이 그룹을 검토 하 여 [그룹의 적응 응용 프로그램 제어 규칙 편집](#editing-a-groups-adaptive-application-controls-rule)에 설명 된 대로 일부 적응 응용 프로그램 제어 규칙을 수동으로 _정의할 수 있는지 여부를 확인_ 하는 것이 좋습니다. Security Center
      >
      > [컴퓨터를 한 그룹에서 다른 그룹으로 이동](#move-a-machine-from-one-group-to-another)에 설명 된 대로이 그룹에서 다른 그룹으로 컴퓨터를 이동할 수도 있습니다.

    - **권장 사항 없음** -정의 된 허용 응용 프로그램 목록이 없는 컴퓨터 이며 기능을 지원 하지 않습니다. 다음과 같은 이유로이 탭에 컴퓨터가 있을 수 있습니다.
      - Log Analytics 에이전트가 없습니다.
      - Log Analytics 에이전트가 이벤트를 보내지 않습니다.
      - GPO 또는 로컬 보안 정책에서 사용 하도록 설정 된 기존 [AppLocker](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) 정책을 사용 하는 Windows 컴퓨터입니다.

      > [!TIP]
      > Security Center는 컴퓨터 그룹당 고유한 권장 사항을 정의 하기 위해 2 주 이상의 데이터가 필요 합니다. 최근에 만들었거나 표준 계층을 사용 하 여 최근에 사용 하도록 설정 된 구독에 속하는 컴퓨터는 **권장 사항 없음** 탭 아래에 표시 됩니다.


1. **권장** 탭을 엽니다. 권장 허용 목록이 있는 컴퓨터 그룹이 표시 됩니다.

   ![권장 탭](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. 그룹을 선택합니다. 

1. 새 규칙을 구성 하려면이 특정 컴퓨터 그룹에 고유 하 게 적용 되는 **응용 프로그램 제어 규칙 구성** 페이지의 다양 한 섹션을 검토 합니다.

   ![새 규칙 구성](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **컴퓨터 선택** -기본적으로 식별 된 그룹의 모든 컴퓨터를 선택 합니다. 이 규칙에서 제거 하려는 항목을 선택 취소 합니다.
   
   1. **권장 되는 응용 프로그램** -이 그룹 내의 컴퓨터에 공통적인 응용 프로그램 목록을 검토 하 여 실행을 허용 하는 것이 좋습니다.
   
   1. **추가 응용 프로그램** -이 그룹 내 컴퓨터에서 자주 표시 되지 않거나 악용 가능한 것으로 알려진 응용 프로그램 목록을 검토 합니다. 경고 아이콘은 공격자가 응용 프로그램 허용 목록을 무시 하는 데 특정 응용 프로그램을 사용할 수 있음을 나타냅니다. 이러한 응용 프로그램을 신중 하 게 검토 하는 것이 좋습니다.

      > [!TIP]
      > 두 응용 프로그램 목록에는 특정 응용 프로그램을 특정 사용자로 제한 하는 옵션이 포함 되어 있습니다. 가능한 경우 최소 권한의 원칙을 채택 합니다.
      > 
      > 응용 프로그램은 게시자에 의해 정의 됩니다. 응용 프로그램에 게시자 정보가 없는 경우 (서명 되지 않은 경우) 특정 응용 프로그램의 전체 경로에 대 한 경로 규칙이 만들어집니다.

   1. 규칙을 적용 하려면 **감사**를 선택 합니다. 




## <a name="editing-a-groups-adaptive-application-controls-rule"></a>그룹의 적응 응용 프로그램 제어 규칙 편집

조직의 알려진 변경 내용으로 인해 컴퓨터 그룹에 대 한 허용 목록을 편집 하도록 결정할 수 있습니다. 

컴퓨터 그룹에 대 한 규칙을 편집 하려면:

1. Security Center의 메뉴에서 **적응 응용 프로그램 컨트롤**을 선택 합니다.

1. **구성 됨** 탭에서 편집 하려는 규칙이 포함 된 그룹을 선택 합니다.

1. [컴퓨터 그룹에서 적응 응용 프로그램 제어 사용](#enable-application-controls-on-a-group-of-machines)에 설명 된 대로 **응용 프로그램 제어 규칙 구성** 페이지의 다양 한 섹션을 검토 합니다.

1. 필요에 따라 하나 이상의 사용자 지정 규칙을 추가 합니다.

   1. **규칙 추가**를 선택 합니다.

      ![사용자 지정 규칙 추가](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. 알려진 안전한 경로를 정의 하는 경우 **규칙 유형을** ' 경로 '로 변경 합니다. 경로에 와일드 카드를 포함할 수 있습니다.
   
      > [!TIP]
      > 경로의 와일드 카드가 유용할 수 있는 몇 가지 시나리오는 다음과 같습니다.
      > 
      > * 경로 끝에 와일드 카드를 사용 하 여이 폴더와 하위 폴더에 있는 모든 실행 파일을 허용 합니다.
      > * 경로 중간에 와일드 카드를 사용 하 여 변경 된 폴더 이름 (예: 알려진 실행 파일, 자동으로 생성 된 폴더 이름 등을 포함 하는 개인 사용자 폴더)으로 알려진 실행 파일 이름 사용
  
   1. 허용 되는 사용자 및 보호 된 파일 형식을 정의 합니다.

   1. 규칙 정의를 완료 한 후 **추가**를 선택 합니다.

1. 변경 내용을 적용 하려면 **저장**을 선택 합니다.




## <a name="responding-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>"적응 응용 프로그램 제어 정책의 Allowlist 규칙을 업데이트 해야 합니다." 권장 사항

이 권장 사항은 이전에는 허용 되지 않은 잠재적으로 합법적인 동작을 Security Center의 machine learning에서 식별 하는 경우에 나타납니다. 권장 사항은 거짓 긍정 경고 수를 줄이기 위해 기존 정의에 대 한 새 규칙을 제안 합니다.

문제를 해결 하려면 다음을 수행 합니다.

1. 권장 사항 페이지에서 **적응 응용 프로그램 제어 정책에서 Allowlist 규칙** 을 선택 하 여 새로 식별 된 잠재적으로 합법적인 동작을 포함 하는 그룹을 확인 합니다.

1. 편집 하려는 규칙이 포함 된 그룹을 선택 합니다.

1. [컴퓨터 그룹에서 적응 응용 프로그램 제어 사용](#enable-application-controls-on-a-group-of-machines)에 설명 된 대로 **응용 프로그램 제어 규칙 구성** 페이지의 다양 한 섹션을 검토 합니다.

1. 변경 내용을 적용 하려면 **감사**를 선택 합니다.




## <a name="auditing-alerts-and-violations"></a>경고 및 위반 감사

1. Security Center의 메뉴에서 **적응 응용 프로그램 컨트롤**을 선택 합니다.

1. 최근 경고가 있는 컴퓨터의 그룹을 확인 하려면 **구성 됨** 탭에 나열 된 그룹을 검토 합니다.

1. 자세히 조사 하려면 그룹을 선택 합니다.

   ![최근 경고](./media/security-center-adaptive-application/recent-alerts.png)

1. 자세한 내용과 영향을 받는 컴퓨터의 목록에서 경고를 선택 합니다.



## <a name="move-a-machine-from-one-group-to-another"></a>컴퓨터를 한 그룹에서 다른 그룹으로 이동

컴퓨터를 한 그룹에서 다른 그룹으로 이동 하면 해당 컴퓨터에 적용 된 응용 프로그램 제어 정책이 사용자가 이동한 그룹의 설정으로 변경 됩니다. 구성 된 그룹에서 구성 되지 않은 그룹으로 컴퓨터를 이동할 수도 있습니다. 이렇게 하면 컴퓨터에 적용 된 모든 응용 프로그램 제어 규칙이 제거 됩니다.

1. **적응 응용 프로그램 컨트롤** 페이지의 **구성 됨** 탭에서 이동할 컴퓨터를 포함 하는 그룹을 선택 합니다.

1. **구성 된 컴퓨터**의 목록을 엽니다.

1. 행의 끝에 있는 세 개의 점으로 컴퓨터의 메뉴를 열고 **이동**을 선택 합니다. **컴퓨터를 다른 그룹으로 이동** 창이 열립니다.

1. 대상 그룹을 선택 하 고 **컴퓨터 이동**을 선택 합니다.

1. 변경 내용을 저장 하려면 **저장**을 선택 합니다.





## <a name="managing-application-controls-via-the-rest-api"></a>REST API를 통해 응용 프로그램 컨트롤 관리 

적응 응용 프로그램 컨트롤을 프로그래밍 방식으로 관리 하려면 REST API을 사용 합니다. 

전체 API 설명서는 [여기](https://docs.microsoft.com/rest/api/securitycenter/adaptiveapplicationcontrols)에 있습니다.

REST API에서 사용할 수 있는 일부 함수는 다음과 같습니다.

* **목록** 모든 그룹 권장 사항을 검색 하 고 각 그룹에 대 한 개체를 사용 하 여 JSON을 제공 합니다.

* **Get** 은 전체 권장 사항 데이터 (즉, 컴퓨터 목록, 게시자/경로 규칙 등)를 사용 하 여 JSON을 검색 합니다.

* **Put** 은 규칙을 구성 합니다. **Get** 을 사용 하 여 검색 한 JSON을이 요청에 대 한 본문으로 사용 합니다.
 
   > [!IMPORTANT]
   > **Put** 함수에는 Get 명령에 의해 반환 된 JSON 보다 더 많은 매개 변수가 필요 합니다.
   >
   > Put 요청에서 JSON을 사용 하기 전에 recommendationStatus, configurationStatus, 문제, 위치 및 sourceSystem 속성을 제거 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center에서 적응 응용 프로그램 제어를 사용 하 여 Azure 및 비 Azure 컴퓨터에서 실행 되는 응용 프로그램의 허용 목록을 정의 하는 방법을 배웠습니다. Security Center의 다른 클라우드 워크 로드 보호 기능에 대 한 자세한 내용은 다음을 참조 하세요.

* [JIT (just-in-time) VM 액세스 이해](just-in-time-explained.md)
* [Azure Kubernetes 클러스터 보안 설정](azure-kubernetes-service-integration.md)