---
title: Azure Security Center를 사용하여 Endpoint Protection 문제 관리 | Microsoft Docs
description: Azure Security Center에서 Endpoint Protection 문제를 관리하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 882d4e0592b74e8af30ff5bf110a41e403c3bf7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906579"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Azure Security Center를 사용하여 Endpoint Protection 문제 관리
Azure Security Center는 맬웨어 방지 보호의 상태를 모니터링하여 Endpoint Protection 문제 블레이드 아래에서 해당 내용을 보고합니다. Security Center에서는 VM(가상 머신) 및 컴퓨터를 맬웨어 방지 위협에 취약하게 만들 수 있는 검색된 위협, 불충분한 보호 수준 등의 문제가 강조 표시됩니다. **Endpoint Protection 문제** 아래의 정보를 사용하면 확인된 문제를 해결하기 위한 계획을 파악할 수 있습니다.

Security Center에서 보고하는 Endpoint Protection 문제는 다음과 같습니다.

- Azure VM에 Endpoint Protection이 설치되지 않음 - 이러한 Azure VM에 지원되는 맬웨어 방지 솔루션이 설치되어 있지 않습니다.
- 비 Azure 컴퓨터에 Endpoint Protection이 설치되지 않음 - 이러한 비 Azure 컴퓨터에 지원되는 맬웨어 방지 솔루션이 설치되어 있지 않습니다.
- Endpoint Protection 상태:

  - 만료된 서명 - 이러한 VM과 컴퓨터에 맬웨어 방지 솔루션이 설치되어 있기는 하지만 솔루션에 최신 맬웨어 방지 서명이 없습니다.
  - 실시간 보호 없음 - 이러한 VM과 컴퓨터에 맬웨어 방지 솔루션이 설치되어 있기는 하지만 해당 솔루션이 실시간 보호용으로 구성되어 있지 않습니다.   서비스가 사용하지 않도록 설정되어 있거나, 솔루션이 지원되지 않아 Security Center가 상태를 가져올 수 없는 것일 수 있습니다. 지원되는 솔루션 목록은 [파트너 통합](security-center-os-coverage.md#supported-endpoint-protection-solutions)을 참조하세요.
  - 보고하지 않음 - 맬웨어 방지 솔루션이 설치되어 있기는 하지만 데이터를 보고하지 않습니다.
  - 알 수 없음 - 맬웨어 방지 솔루션이 설치되어 있기는 하지만 해당 상태를 알 수 없거나, 알 수 없는 오류가 보고됩니다.

    > [!NOTE]
    > Security Center와 통합된 엔드포인트 보호 보안 솔루션 목록은 [보안 솔루션 통합](security-center-os-coverage.md#supported-endpoint-protection-solutions)을 참조하세요.
    >
    >

## <a name="implement-the-recommendation"></a>권장 사항 구현
Endpoint Protection은 Security Center에서 권장 사항으로 표시됩니다.  환경이 맬웨어 방지 위협에 취약한 경우 **권장 사항**과 **계산** 아래에 이 권장 사항이 표시됩니다. **Endpoint Protection 문제 대시보드**를 보려면 계산 워크플로를 따라야 합니다.

이 예제에서는 **계산**을 사용합니다.  Azure VM 및 비 Azure 컴퓨터에 맬웨어 방지 솔루션을 설치하는 방법을 살펴보겠습니다.

## <a name="install-antimalware-on-azure-vms"></a>Azure VM에 맬웨어 방지 솔루션 설치

1. 선택 **Compute 및 앱** Security Center 주 메뉴에서 또는 **개요**합니다.

   ![계산 선택][1]

2. **계산** 아래에서 **Endpoint Protection 문제**를 선택합니다. **Endpoint Protection 문제** 대시보드가 열립니다.

   ![Endpoint Protection 문제 선택][2]

   대시보드 위쪽에는 다음 정보가 제공됩니다.

   - 설치된 Endpoint Protection 공급자 - Security Center에서 식별한 여러 공급자가 나열됩니다.
   - 설치된 Endpoint Protection 상태 - Endpoint Protection 솔루션이 설치된 VM과 컴퓨터의 상태가 표시됩니다. 차트에는 정상 상태인 VM/컴퓨터의 수와 보호 수준이 불충분한 VM/컴퓨터의 수가 표시됩니다.
   - 맬웨어 검색됨 - Security Center에서 검색된 맬웨어를 보고하는 VM 및 컴퓨터의 수가 표시됩니다.
   - 공격 받은 컴퓨터-Vm 및 Security Center가 보고 하는 공격 맬웨어에서 컴퓨터의 수를 보여 줍니다.

   대시보드 아래쪽에는 다음 정보가 포함된 Endpoint Protection 문제 목록이 있습니다.  

   - **합계** - 문제의 영향을 받는 VM과 컴퓨터의 수입니다.
   - 문제의 영향을 받는 VM과 컴퓨터의 수가 집계된 막대가 표시됩니다. 막대의 색을 통해 우선 순위를 확인할 수 있습니다.

      - 빨간색 - 높은 우선 순위이며 즉시 해결해야 합니다.
      - 주황색 - 보통 우선 순위이며 가능한 한 빨리 해결해야 합니다.

3. **Azure VM에 Endpoint Protection이 설치되지 않음**을 선택합니다.

   ![Azure VM에 Endpoint Protection이 설치되지 않음 선택][3]

4. **Azure VM에 Endpoint Protection이 설치되지 않음** 아래에는 맬웨어 방지 솔루션이 설치되지 않은 Azure VM 목록이 있습니다.  목록의 모든 VM에 맬웨어 방지 솔루션을 설치할 수도 있고, 특정 VM을 클릭하여 맬웨어 방지 솔루션을 설치할 개별 VM을 선택할 수도 있습니다.
5. **Endpoint Protection 선택**에서 사용할 Endpoint Protection 솔루션을 선택합니다. 이 예제에서는 **Microsoft 맬웨어 방지 프로그램**을 선택합니다.
6. Endpoint Protection 솔루션에 대한 추가 정보가 표시됩니다. **만들기**를 선택합니다.

## <a name="install-antimalware-on-non-azure-computers"></a>비 Azure 컴퓨터에 맬웨어 방지 솔루션 설치

1. **Endpoint Protection 문제**로 돌아가서 **비 Azure 컴퓨터에 Endpoint Protection이 설치되지 않음**을 선택합니다.

   ![비 Azure 컴퓨터에 Endpoint Protection이 설치되지 않음 선택][4]

2. **비 Azure 컴퓨터에 Endpoint Protection이 설치되지 않음**에서 작업 영역을 선택합니다. 작업 영역으로 필터링 하는 Azure Monitor 로그 검색 쿼리가 열리고 맬웨어 방지 누락 된 컴퓨터를 나열 합니다. 목록에서 컴퓨터를 선택하면 자세한 내용을 확인할 수 있습니다.

   ![Azure Monitor의 로그 검색][5]

해당 컴퓨터와 관련된 정보만 표시되도록 필터링된 다른 검색 결과가 열립니다.

  ![Azure Monitor의 로그 검색][6]

> [!NOTE]
> 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 파악하여 제거할 수 있도록 모든 VM과 컴퓨터에 대해 Endpoint Protection을 프로비전하는 것이 좋습니다.
>
>

## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터 권장 사항 "Endpoint Protection 설치"를 구현하는 방법을 보여 주었습니다. Azure에서 Microsoft 맬웨어 방지 프로그램을 사용하도록 설정하는 방법에 대해 알아보려면 다음 문서를 참조하세요.

* [Cloud Services 및 Virtual Machines용 Microsoft 맬웨어 방지 프로그램](../security/azure-security-antimalware.md) -- Microsoft 맬웨어 방지 프로그램을 배포하는 방법을 알아봅니다.

Security Center에 대해 알아보려면 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) -- Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
