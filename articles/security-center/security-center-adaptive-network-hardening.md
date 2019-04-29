---
title: Azure Security Center에서 적응 네트워크 강화 | Microsoft Docs
description: " Azure Security Center에서 적응 네트워크 강화를 사용 하는 방법에 알아봅니다. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706608"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure Security Center에서 적응 네트워크 강화
Azure Security Center에서 적응 네트워크 강화를 구성 하는 방법에 알아봅니다.

## <a name="what-is-adaptive-network-hardening"></a>적응 네트워크 강화 하는 것이 무엇입니까?
적용 [네트워크 보안 그룹 (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) 리소스 간의 트래픽을 필터링 하기 위해 네트워크 보안 태세를 향상 합니다. 그러나 있을 수 있습니다 여전히 몇 가지는 경우 NSG를 통해 이동 하는 트래픽의 실제 정의 된 NSG 규칙의 하위 집합입니다. 이러한 경우 추가 보안 태세를 향상 함으로써 가능 실제 트래픽 패턴을 기반으로 하는 NSG 규칙을 강화 합니다.

적응 네트워크 강화는 NSG 규칙은 강화 권장 사항을 제공 합니다. 기계 학습 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상 지표를 알려진 실제 트래픽을 고려 하는 알고리즘을 사용 하 고 특정 i P/포트 튜플 트래픽만 허용 되는 권장 사항을 제공 합니다.

예를 들어 기존 NSG 규칙 140.20.30.10/24 22 포트로에서 트래픽을 허용 하는 것을 가정해 보겠습니다. 적응 네트워크 강화의 권장 사항에는 분석을 기반으로 범위를 좁힐 IP 범위를 좁은 인 140.23.30.10/29 – 트래픽을 허용 하 고 해당 포트에 다른 모든 트래픽은 거부 됩니다.

![네트워크 강화 보기](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> 적응 네트워크 강화 권장 사항은 다음 포트에서 지원 됩니다. 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>적응 네트워크 강화 경고 보기 및 규칙

1. Security Center에서 선택 **네트워킹** -> **적응 네트워크 강화**합니다. Vm 네트워크는 3 개의 개별 탭 아래에서 나열 됩니다.
   * **비정상 리소스**: 권장 사항 및 적응 네트워크 강화 알고리즘을 실행 하 여 트리거된 경고는 현재 있는 Vm을 선택 합니다. 
   * **정상 리소스**: Vm 경고 및 권장 사항 없음입니다.
   * **리소스를 검색 되지 않고 발송**: 다음 이유 중 하나로 인해 네트워크 강화 적응 알고리즘을 실행할 수 없는 Vm:
      * **Vm은 클래식 Vm**:-Azure Resource Manager Vm만 지원 됩니다.
      * **데이터가 충분 하지 않습니다 수**: Security Center는 정확 하 게 트래픽을 강화 하는 권장을 생성 하려면 트래픽 데이터의 30 일 이내에 필요 합니다.
      * **ASC 표준으로 VM 보호 되지 않으므로**: Security Center의 표준 가격 책정 계층으로 설정 된 Vm만이 기능에 적합 합니다.

     ![비정상 리소스](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. **비정상 리소스** 탭의 경고 및 권장 되는 보안 강화 적용할 규칙을 확인 하는 VM을 선택 합니다.

    ![강화 알림](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>검토 및 적응 네트워크 강화 권장 규칙 적용

1. **비정상 리소스** 탭에서 VM을 선택 합니다. 경고 및 권장 되는 보안 강화 규칙 나열 됩니다.
   ![강화 알림](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > 합니다 **규칙** 탭 추가한 권장 적응 네트워크 강화 하는 규칙을 나열 합니다. 합니다 **경고** 탭 트래픽, 권장 되는 규칙에 허용 되는 IP 범위에 속하지는 리소스의 이동으로 인해 생성 된 경고를 나열 합니다.

   ![강화 규칙](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. 일부 규칙의 매개 변수를 변경 하려는 경우 수정할 수 있습니다에 설명 된 대로 [규칙을 수정](#modify-rule)합니다.
   > [!NOTE]
   > 할 수도 있습니다 [삭제할](#delete-rule) 하거나 [추가](#add-rule) 규칙입니다.

3. 규칙을 NSG에 적용 하 고 클릭 하려는 선택할 **적용**합니다. 

### 규칙 수정  <a name ="modify-rule"> </a>

권장 된에 규칙의 매개 변수를 수정 하려는 경우. 예를 들어, 다음 권장 되는 IP 범위를 변경 하는 것이 좋습니다.

적응 네트워크 강화 규칙을 수정 하기 위한 몇 가지 중요 한 지침:

* "허용" 규칙의 매개 변수를 수정할 수 있습니다. 
* "허용" 규칙 "거부" 규칙 되도록 변경할 수 없습니다. 

  > [!NOTE]
  > 만들기 및 수정 "거부" 규칙에서 수행 됩니다 직접는 NSG에 대 한 자세한 내용은 [만들기, 변경 또는 네트워크 보안 그룹을 삭제](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)합니다.

* A **모든 트래픽을 거부** 규칙은 여기에 나열 됩니다 "거부" 규칙의 유일한 유형 및 수정할 수 없습니다. 수 있지만 삭제할 수 있습니다 (참조 [규칙을 삭제](#delete-rule)).
  > [!NOTE]
  > A **모든 트래픽을 거부** 규칙은 권장 되는 경우, 결과적으로 알고리즘을 실행 하는 Security Center를 식별 하지 않습니다 트래픽을 허용 하는 수를 기존 NSG 구성에 따라 합니다. 따라서 권장 되는 규칙은 지정된 된 포트를 모든 트래픽을 거부 합니다. 이 유형의 규칙의 이름은 "시스템 생성"으로 표시 됩니다. 이 규칙을 적용 한 후 NSG의 실제 이름과 프로토콜, 트래픽 방향, "거부" 및 임의 숫자로 구성 된 문자열로 됩니다.

*적응 네트워크 강화 규칙을 수정 합니다.*

1. 규칙의 매개 변수 중 일부를 수정 해야 합니다 **규칙** 탭에서 규칙의 행 끝에 줄임표 (...)를 클릭 하 고 클릭 **규칙 편집**합니다.

   ![규칙 편집](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 에 **규칙 편집** 창에서 변경 하 고 클릭 하려는 세부 정보를 업데이트 **저장**합니다.

   > [!NOTE]
   > 클릭 한 후 **저장할**, 규칙을 변경 했습니다. *그러나 적용 하지 않은 것을 NSG에 있습니다.* 이 적용 하려면 목록에서 규칙을 선택 및 클릭 해야 합니다 **적용** (설명 했 듯이 다음 단계에서).

3. 목록에서 업데이트 된 규칙을 적용할 업데이트 된 규칙을 선택 하 고 클릭 **적용**합니다.

### 새 규칙 추가 <a name ="add-rule"> </a>


Security Center에서 권장 되지 되었던 "허용" 규칙을 추가할 수 있습니다.

> [!NOTE]
> "허용" 규칙 여기에 추가할 수 있습니다. "거부" 규칙을 추가 하려는 경우 NSG에서 직접 수행할 수 있습니다. 자세한 내용은 참조 하세요. [만들기, 변경 또는 네트워크 보안 그룹을 삭제](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)합니다.

*적응 네트워크 강화 규칙을 추가 합니다.*

1. 클릭 **규칙 추가** (왼쪽된 위 모서리에 있음).

   ![규칙 추가](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 에 **규칙 편집** 창에서 세부 정보를 입력 하 고 클릭 **저장**합니다.

   > [!NOTE]
   > 클릭 한 후 **저장할**, 규칙을 성공적으로 추가한 및 다른 권장 되는 규칙을 사용 하 여 나열 됩니다. 그러나 적용 하지 않은 해당 NSG에 있습니다. 서비스를 활성화 하려면 목록에서 규칙을 선택 및 클릭 해야 합니다 **적용** (설명 했 듯이 다음 단계에서).

3. 목록에서 새 규칙을 적용할 새 규칙을 선택 하 고 클릭 **적용**합니다.



### 규칙 삭제 <a name ="delete-rule"> </a>

필요한 경우 권장 되는 규칙을 삭제할 수 있습니다. 예를 들어, 제안 된 규칙을 적용 정당한 트래픽을 차단할 수를 결정할 수 있습니다.

*적응 네트워크 강화 규칙을 삭제 합니다.*

1. 에 **규칙** 탭에서 규칙의 행 끝에 줄임표 (...)를 클릭 하 고 클릭 **삭제 규칙**합니다.

   ![규칙 삭제](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

