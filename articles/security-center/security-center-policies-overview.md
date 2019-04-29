---
title: Azure Security Center 설정 | Microsoft Docs
description: Azure Security Center 설정을 구성합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703526"
---
# <a name="security-center-settings"></a>Security Center 설정
이 문서에서는 Security Center의 설정에 대한 개요를 제공합니다.

다음 설정은 보안 정책에서 찾을 수 있습니다.

- **데이터 컬렉션**: 에이전트 프로비저닝 및 [데이터 컬렉션](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) 설정을 결정합니다.
- **보안 정책**: Security Center에서 모니터링 및 권장하는 컨트롤을 결정합니다. Security Center에서 [보안 정책](tutorial-security-policy.md)을 편집할 수 있습니다. [Azure Policy](tutorial-security-policy.md)를 사용하여 새 정의를 만들고, 추가 정책을 정의하고, 관리 그룹에 걸쳐 정책을 할당할 수도 있습니다. 
- **메일 알림**: 보안 연락처 및 [메일 알림](security-center-provide-security-contact-details.md) 설정을 결정합니다.
- **가격 책정 계층**: 무료 또는 표준 [가격 선택](security-center-pricing.md)을 정의합니다. 선택한 계층에 따라 범위의 리소스에 대해 사용할 수 있는 Security Center 기능이 결정됩니다. 구독 및 작업 영역에 대 한 계층을 지정할 수 있습니다.

> [!NOTE]
> 이러한 모든 항목을 구독별로 설정할 수 있습니다. 작업 영역의 경우 데이터 컬렉션 및 가격 책정 계층만 설정할 수 있습니다.
>


## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?
Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 사용자의 역할에 할당 되는 *소유자*를 *참가자*, 또는 *판독기* 리소스가 속한 구독에 있습니다. 이러한 역할 외에도 두 개의 특정한 Security Center 역할이 있습니다.

- **보안 읽기 권한자**: 권장 사항, 경고, 정책 및 상태를 비롯한 Security Center에 대한 권한을 볼 수 있지만 변경할 수는 없습니다.
- **보안 관리자**: *보안 읽기 권한자*와 동일한 보기 권한을 갖지만 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center의 보안 정책에 대해 알아보았습니다. Azure Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure 구독에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md): Security Center 권장 사항을 통해 Azure 리소스를 보호하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center 데이터 보안](security-center-data-security.md): Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/): 최신 Azure 보안 뉴스 및 정보를 가져옵니다.
