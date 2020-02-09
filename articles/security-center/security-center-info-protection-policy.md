---
title: SQL information protection 사용자 지정-Azure Security Center
description: Azure Security Center에서 정보 보호 정책을 사용자 지정하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611069"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Azure Security Center에서 SQL 정보 보호 정책 사용자 지정
 
Azure Security Center에서 전체 Azure 테넌트에 대 한 SQL information protection 정책을 정의 하 고 사용자 지정할 수 있습니다.

Information protection은 Azure 데이터 리소스에서 중요 한 데이터를 검색, 분류, 레이블 지정 및 보고 하는 고급 보안 기능입니다. 가장 중요 한 데이터 (비즈니스, 재무, 의료, 개인 데이터 등)를 검색 하 고 분류 하면 조직 정보 보호에서 pivotal 역할을 수행할 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.
- 데이터 개인 정보 보호 표준 및 규정 준수 요구 사항 충족 지원
- 모니터링 (감사) 및 중요 한 데이터에 대 한 비정상적인 액세스 경고와 같은 보안 시나리오
- 매우 중요한 데이터가 들어 있는 데이터 저장소에 대한 액세스 제어 및 보안 강화
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md)은 SQL 데이터 저장소에 대한 이 패러다임을 구현하며 현재 Azure SQL Database에 지원됩니다. SQL Information Protection은 잠재적으로 중요한 데이터를 자동으로 검색하고 분류하며, 분류 속성으로 중요한 데이터의 태그를 영구적으로 지정하는 레이블 지정 메커니즘을 제공하고, 데이터베이스 분류 상태를 보여주는 상세 대시보드를 제공합니다. 또한 중요한 데이터를 추출하는 쿼리를 명시적으로 감사할 수 있도록 SQL 쿼리의 결과 집합 민감도를 계산하고 데이터를 보호할 수 있습니다. SQL Information Protection에 대 한 자세한 내용은 [데이터 검색 및 분류 Azure SQL Database](../sql-database/sql-database-data-discovery-and-classification.md)를 참조 하세요.
 
분류 메커니즘은 분류를 구성하는 **레이블** 및 **정보 형식**이라는 두 가지 기본 구문을 기반으로 합니다.
- **레이블** - 열에 저장된 데이터의 민감도 수준을 정의하는 데 사용되는 주요 분류 속성입니다. 
- **정보 형식** – 열에 저장된 데이터의 형식에 추가 세분성을 제공합니다.
 
Information Protection은 기본적으로 사용되는 레이블 및 정보 형식의 기본 제공 집합이 함께 제공됩니다. 이러한 레이블 및 유형을 사용자 지정 하려면 Security Center에서 information protection 정책을 사용자 지정할 수 있습니다.
 
## <a name="customize-the-information-protection-policy"></a>정보 보호 정책 사용자 지정
Azure 테넌트에 대한 정보 보호 정책을 사용자 지정하려면 [테넌트의 루트 관리 그룹에 대한 관리 권한](security-center-management-groups.md)이 있어야 합니다. 
 
1. Security Center 주 메뉴의 **리소스 보안** 관리에서 **데이터 & 저장소** 로 이동 하 고 **SQL Information Protection** 단추를 클릭 합니다.

   ![정보 보호 정책 구성](./media/security-center-info-protection-policy/security-policy.png) 
 
2. **SQL Information Protection** 페이지에서 현재 레이블 집합을 볼 수 있습니다. 데이터의 민감도 수준을 분류하는 데 사용되는 주요 분류 속성입니다. 여기에서부터 테넌트에 대한 **정보 보호 레이블** 및 **정보 형식**을 구성할 수 있습니다. 
 
### <a name="customizing-labels"></a>레이블 사용자 지정
 
1. 기존 레이블을 편집하거나, 삭제하거나, 새 레이블을 추가할 수 있습니다. 기존 레이블의 편집하려면 위쪽 또는 오른쪽의 팝업 메뉴에서 해당 레이블을 선택한 다음, **구성**을 클릭합니다. 새 레이블을 추가하려면 상단 메뉴 모음 또는 레이블 테이블의 맨 아래에서 **레이블 만들기**를 클릭합니다.
2. **민감도 레이블 구성** 화면에서 레이블 이름 및 설명을 만들거나 변경할 수 있습니다. **사용** 스위치를 설정/해제하여 레이블이 활성 또는 비활성인지를 설정할 수도 있습니다. 마지막으로 레이블과 관련된 정보 형식을 추가하거나 제거할 수 있습니다. 해당 정보 형식과 일치하는 데이터에는 분류 권장 사항에서 관련된 민감도 레이블이 자동으로 포함됩니다.
3. **확인**을 클릭합니다.
 
   ![민감도 레이블 구성](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. 레이블은 민감도 오름차순 순서로 나열됩니다. 레이블 간의 순위를 변경하려면 레이블을 끌어와서 테이블에서 순서를 변경하거나 **위로 이동** 및 **아래로 이동** 단추를 사용하여 순서를 변경합니다. 
 
    ![정보 보호 정책 구성](./media/security-center-info-protection-policy/move-up.png)
 
5. 작업을 완료하면 화면 맨 위에 있는 **저장**을 클릭해야 합니다.
 
 
## <a name="adding-and-customizing-information-types"></a>정보 형식 추가 및 사용자 지정
 
1. **정보 형식 관리**를 클릭하여 정보 형식을 관리하고 사용자 지정할 수 있습니다.
2. 새 **정보 형식**을 추가하려면 최상위 메뉴에서 **정보 형식 만들기**를 선택합니다. **정보 형식**에 대한 이름, 설명 및 검색 패턴 문자열을 구성할 수 있습니다. 필요에 따라 검색 패턴 문자열은 열의 메타데이터에 따라 와일드 카드 문자('%' 문자 사용)와 함께 키워드를 사용할 수 있습니다. 이 항목은 자동화된 검색 엔진이 데이터베이스에서 중요한 데이터를 식별하는 데 사용합니다.
 
    ![정보 보호 정책 구성](./media/security-center-info-protection-policy/info-types.png)
 
3. 추가 검색 패턴 문자열을 추가하거나, 기존 문자열의 일부를 사용하지 않도록 설정하거나, 설명을 변경하여 기본 제공 **정보 형식**을 구성할 수도 있습니다. 기본 제공 **정보 형식**을 삭제하거나 해당 이름을 편집할 수 없습니다. 
4. **정보 형식**은 검색 순위 오름차순으로 나열됩니다. 즉, 목록의 위에 있는 형식을 먼저 일치시킵니다. 정보 형식 간의 순위를 변경하려면 형식을 테이블의 오른쪽으로 끌어오거나 **위로 이동** 및 **아래로 이동** 단추를 사용하여 순서를 변경합니다. 
5. 작업이 완료되면 **확인**을 클릭합니다.
6. 정보 형식 관리를 완료하면 특정 레이블에 대한 **구성**을 클릭하고, 적절하게 정보 형식을 추가하거나 삭제하여 적절한 레이블과 관련 형식을 연결하도록 합니다.
7. 주요 **레이블** 블레이드에서 **저장**을 클릭하여 모든 변경 내용을 적용하도록 합니다.
 
정보 보호 정책이 완벽하게 정의되고 저장되면 테넌트의 모든 Azure SQL 데이터베이스에서 데이터 분류에 적용됩니다.
 
 
## <a name="next-steps"></a>다음 단계
 
이 문서는 Azure Security Center에서 SQL Information Protection 정책을 정의하는 방법에 대해 알아보았습니다. SQL Information Protection을 사용하여 SQL 데이터베이스에서 중요한 데이터를 분류하고 보호하는 방법에 대해 자세히 알아보려면 [Azure SQL Database 데이터 검색 및 분류](../sql-database/sql-database-data-discovery-and-classification.md)를 참조하세요. 

Azure Security Center에서 보안 정책 및 데이터 보안에 대한 자세한 내용은 다음 문서를 참조하세요.
 
- [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md): Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
