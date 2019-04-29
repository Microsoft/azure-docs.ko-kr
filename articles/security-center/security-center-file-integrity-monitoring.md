---
title: Azure Security Center에서 파일 무결성 모니터링 | Microsoft Docs
description: " Azure Security Center에서 파일 무결성 모니터링을 사용하도록 설정하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: f8bc10edcdc31dd2ae3995dcb8321a5523e1e51c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705864"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure Security Center에서 파일 무결성 모니터링
이 연습을 사용하여 Azure Security Center에서 FIM(파일 무결성 모니터링)을 구성하는 방법을 알아봅니다.

## <a name="what-is-fim-in-security-center"></a>Security Center의 FIM이란?
변경 모니터링이라고도 하는 FIM(파일 무결성 모니터링)은 공격을 나타낼 수 있는 변경에 대해 운영 체제, 애플리케이션 소프트웨어 및 다른 요소의 파일과 레지스트리를 검사합니다. 비교 메서드는 파일의 현재 상태가 파일의 마지막 검사와 다른지 확인하는 데 사용됩니다. 이 비교를 활용하여 파일에 대해 유효하거나 의심스러운 수정 사항이 있는지 확인할 수 있습니다.

Security Center의 파일 무결성 모니터링은 Windows 파일, Windows 레지스트리 및 Linux 파일의 무결성을 검사합니다. FIM을 사용하여 모니터링할 파일을 선택합니다. Security Center는 다음과 같은 활동에 대해 사용하도록 설정된 FIM을 통해 파일을 모니터링합니다.

- 파일 및 레지스트리 만들기 및 제거
- 파일 수정(파일 크기, 액세스 제어 목록 및 콘텐츠 해시의 변경)
- 레지스트리 수정(크기, 액세스 제어 목록, 형식 및 콘텐츠의 변경)

Security Center에서는 모니터링할 엔터티를 권장하며 FIM을 쉽게 사용할 수 있습니다. 사용자 고유의 FIM 정책 또는 모니터링할 엔터티를 정의할 수도 있습니다. 이 연습에서는 방법을 보여 줍니다.

> [!NOTE]
> FIM(파일 무결성 모니터링) 기능은 Windows/Linux 컴퓨터 및 VM에서 작동하며 Security Center의 표준 계층에서 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요. FIM은 Log Analytics 작업 영역에 데이터를 업로드합니다. 업로드하는 데이터의 양에 따라 데이터 요금이 부과됩니다. 자세한 내용은 [Log Analytics 가격](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요.

FIM은 Azure 변경 내용 추적 솔루션을 사용하여 환경의 변경 내용을 추적하고 식별합니다. 파일 무결성 모니터링을 사용 해야는 **변경 내용 추적** 종류의 리소스 **솔루션**합니다. 데이터 수집 빈도 정보를 참조 하세요 [데이터 컬렉션 세부 정보 변경 내용 추적](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) Azure 변경 내용 추적에 대 한 합니다.

> [!NOTE]
> 제거 하는 경우는 **변경 내용 추적** 리소스 있습니다 모니터링 Security Center에서 기능 파일 무결성도 해제 됩니다.

## <a name="which-files-should-i-monitor"></a>어떤 파일을 모니터링해야 할까요?
모니터링할 파일을 선택할 때 시스템 및 애플리케이션에 중요한 파일을 고려해야 합니다. 계획 없이 변경할 것으로 예상되지 않는 파일을 선택합니다. 애플리케이션이나 운영 체제(예: 로그 파일 및 텍스트 파일)에서 자주 변경되는 파일을 선택하면 공격을 식별하기 어렵게 만드는 노이즈가 많이 발생합니다.

Security Center에서는 파일 및 레지스트리 변경 내용이 포함된 알려진 공격 패턴에 따라 기본적으로 모니터링해야 하는 파일을 사용하는 것이 좋습니다.

## <a name="using-file-integrity-monitoring"></a>파일 무결성 모니터링 사용
1. **Security Center** 대시보드를 엽니다.
2. 왼쪽 창의 **고급 클라우드 방어** 아래에서 **파일 무결성 모니터링**을 선택합니다.
![Security Center 대시보드][1]

**파일 무결성 모니터링**이 열립니다.
  ![Security Center 대시보드][2]

각 작업 영역에 대해 제공되는 정보는 다음과 같습니다.

- 지난 주에 발생한 총 변경 횟수(작업 영역에서 FIM을 사용하지 않는 경우 대시(-)가 표시될 수 있음)
- 작업 영역에 보고하는 총 컴퓨터 및 VM 수
- 작업 영역의 지리적 위치
- 작업 영역이 속한 Azure 구독

또한 작업 영역에 대해 표시될 수 있는 단추는 다음과 같습니다.

- ![사용 아이콘:][3] 작업 영역에 대해 FIM을 사용할 수 없음을 나타냅니다. 작업 영역을 선택하면 작업 영역 아래의 모든 컴퓨터에서 FIM을 사용하도록 설정할 수 있습니다.
- ![업그레이드 계획 아이콘][4]: Security Center의 표준 계층에서 작업 영역 또는 구독이 실행되고 있지 않음을 나타냅니다. FIM 기능을 사용하려면 구독에서 표준을 실행해야 합니다.  작업 영역을 선택하면 표준으로 업그레이드할 수 있습니다. 표준 계층 및 업그레이드하는 방법에 대한 자세한 내용은 [보안 강화를 위한 Security Center의 표준 계층으로 업그레이드](security-center-pricing.md)를 참조하세요.
- 공백(단추 없음)은 이미 작업 영역에서 FIM을 사용할 수 있음을 나타냅니다.

**파일 무결성 모니터링** 아래에서 작업 영역을 선택하여 해당 작업 영역에 대해 FIM을 사용하도록 설정하거나, 해당 작업 영역에 대한 파일 무결성 모니터링 대시보드를 보거나, 작업 영역을 표준으로 [업그레이드](security-center-pricing.md)할 수 있습니다.

## <a name="enable-fim"></a>FIM 사용
작업 영역에서 FIM을 사용하도록 설정하려면

1. **파일 무결성 모니터링** 아래에서 **사용** 단추를 사용하여 작업 영역을 선택합니다.
2. 작업 영역에 속한 Windows 및 Linux 컴퓨터 수를 표시하는 **파일 무결성 모니터링 사용**이 열립니다.

   ![파일 무결성 모니터링 사용][5]

   또한 Windows 및 Linux에 권장되는 설정도 나열됩니다.  권장 항목의 전체 목록을 보려면 **Windows 파일**, **레지스트리** 및 **Linux 파일**을 펼칩니다.

3. FIM을 적용하지 않으려는 권장 엔터티를 모두 선택 취소합니다.
4. **파일 무결성 모니터링 적용**을 선택하여 FIM을 사용하도록 설정합니다.

> [!NOTE]
> 언제든지 설정을 변경할 수 있습니다. 자세한 내용은 아래의 모니터링되는 엔터티 편집을 참조하세요.
>
>

## <a name="view-the-fim-dashboard"></a>FIM 대시보드 보기
FIM을 사용하는 작업 영역에 대한 **파일 무결성 모니터링** 대시보드가 표시됩니다. 작업 영역에서 FIM을 사용하도록 설정한 후이거나 FIM이 이미 사용되는 **파일 무결성 모니터링** 창에서 작업 영역을 선택하면 FIM 대시보드가 열립니다.

![파일 무결성 모니터링 대시보드][6]

작업 영역에 대한 FIM 대시보드에서 표시하는 항목은 다음과 같습니다.

- 작업 영역에 연결된 총 컴퓨터 수
- 선택한 기간 동안 발생한 총 변경 횟수
- 변경 유형에 대한 분석(파일, 레지스트리)
- 변경 범주에 대한 분석(수정됨, 추가됨, 제거됨)

대시보드 위쪽의 [필터]를 선택하면 변경 내용을 확인하려는 기간을 적용할 수 있습니다.

![기간 필터][7]

**컴퓨터** 탭(위 그림 참조)에는 이 작업 영역에 보고하는 모든 컴퓨터가 나열됩니다. 대시보드에서 각 컴퓨터에 대해 나열하는 항목은 다음과 같습니다.

- 선택한 기간 동안 발생한 총 변경 횟수
- 파일 변경 또는 레지스트리 변경으로 인한 총 변경 횟수에 대한 분석

검색 필드에서 컴퓨터 이름을 입력하거나 [컴퓨터] 탭 아래에 나열된 컴퓨터를 선택하면 **로그 검색**이 열립니다. 로그 검색은 컴퓨터에 대해 선택한 기간 동안 변경된 모든 내용을 표시합니다. 자세한 내용을 확인하려면 변경 내용을 펼칠 수 있습니다.

![로그 검색][8]

**변경** 탭(아래 그림 참조)에는 선택한 기간 동안 작업 영역에 대한 모든 변경 내용이 나열됩니다. 대시보드에서 변경된 각 엔터티에 대해 나열하는 항목은 다음과 같습니다.

- 변경이 발생한 컴퓨터
- 변경 유형(레지스트리 또는 파일)
- 변경 범주(수정됨, 추가됨, 제거됨)
- 변경 날짜 및 시간

![작업 영역에 대한 변경 내용][9]

검색 필드에서 변경 내용을 입력하거나 **변경** 탭 아래에 나열된 항목을 선택하면 **변경 세부 정보**가 열립니다.

![변경 세부 정보][10]

## <a name="edit-monitored-entities"></a>모니터링된 엔터티 수정

1. **파일 무결성 모니터링 대시보드**로 돌아가서 **설정**을 선택합니다.

   ![설정][11]

   **작업 영역 구성**이 열리고 세 개의 탭 **Windows 레지스트리**, **Windows 파일** 및 **Linux 파일**이 표시됩니다. 각 탭에는 해당 범주에서 편집할 수 있는 엔터티가 나열됩니다. Security Center에서는 나열된 각 엔터티에 대해 FIM을 사용하거나(true) 사용하지 않도록(false) 설정되었는지를 식별합니다.  엔터티를 편집하면 FIM을 사용하거나 사용하지 않도록 설정할 수 있습니다.

   ![작업 영역 구성][12]

2. Id 보호를 선택 합니다. 이 예에서는 Windows 레지스트리에 속한 항목을 선택했습니다. **변경 내용 추적 편집**에 대한 편집이 열립니다.

   ![변경 내용 추적 편집][13]

**변경 내용 추적 편집**에서 수행할 수 있는 작업은 다음과 같습니다.

- 파일 무결성 모니터링 사용 설정(True) 또는 해제(False)
- 엔터티 이름 제공 또는 변경
- 값 또는 경로 제공 또는 변경
- 엔터티 삭제, 변경 내용 삭제 또는 변경 내용 저장

## <a name="add-a-new-entity-to-monitor"></a>모니터링할 새 엔터티 추가
1. **파일 무결성 모니터링 대시보드**로 돌아가서 위쪽에 있는 **설정**을 선택합니다. **작업 영역 구성**이 열립니다.
2. **작업 영역 구성** 아래에서 추가하려는 엔터티의 형식에 대한 탭(Windows 레지스트리, Windows 파일 또는 Linux 파일)을 선택합니다. 이 예에서는 **Linux 파일**을 선택했습니다.

   ![모니터링할 새 항목 추가][14]

3. **추가**를 선택합니다. **변경 내용 추적에 추가**가 열립니다.

   ![요청된 정보 입력][15]

4. **추가** 페이지에서 요청된 정보를 입력하고, **저장**을 선택합니다.

## <a name="disable-monitored-entities"></a>모니터링된 엔터티 사용 안 함
1. **파일 무결성 모니터링** 대시보드로 돌아갑니다.
2. FIM이 현재 사용되는 작업 영역을 선택합니다. [사용] 단추 또는 [업그레이드 계획] 단추가 없으면 작업 영역을 FIM에 사용할 수 있습니다.

   ![FIM이 사용되는 작업 영역 선택][16]

3. [파일 무결성 모니터링] 아래에서 **설정**을 선택합니다.

   ![설정 선택][17]

4. **작업 영역 구성** 아래에서 **사용**이 [참]으로 설정된 그룹을 선택합니다.

   ![작업 영역 구성][18]

5. **변경 내용 추적 편집** 창 아래에서 **사용**을 [거짓]으로 설정합니다.

   ![[거짓]으로 설정된 [사용]][19]

6. **저장**을 선택합니다.

## <a name="folder-and-path-monitoring-using-wildcards"></a>와일드 카드를 사용하여 폴더 및 경로 모니터링

와일드 카드를 사용하여 디렉터리에 대한 추적을 간소화합니다. 와일드 카드를 사용하여 폴더 모니터링을 구성한 경우 다음 규칙이 적용됩니다.
-   와일드 카드는 여러 파일을 추적하는 데 필요합니다.
-   C:\folder\file or /etc/*.conf와 같은 경로의 마지막 세그먼트에만 와일드 카드를 사용할 수 있습니다.
-   환경 변수에 유효하지 않은 경로가 포함된 경우 인벤토리를 실행하면 유효성 검사는 성공하지만 경로는 실패합니다.
-   경로를 설정할 때 c:\*.*와 같은 일반 경로를 사용하면 너무 많은 폴더가 트래버스되므로 사용하지 않는 것이 좋습니다.

## <a name="disable-fim"></a>FIM 사용 안 함
FIM을 사용하지 않도록 설정할 수 있습니다. FIM은 Azure 변경 내용 추적 솔루션을 사용하여 환경의 변경 내용을 추적하고 식별합니다. FIM을 사용하지 않도록 설정하면 선택한 작업 영역에서 변경 내용 추적 솔루션이 제거됩니다.

1. FIM을 사용하지 않도록 설정하려면 **파일 무결성 모니터링** 대시보드로 돌아갑니다.
2. 작업 영역을 선택합니다.
3. **파일 무결성 모니터링** 아래에서 **사용 안 함**을 선택합니다.

   ![FIM 사용 안 함][20]

4. **제거**를 선택하여 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 FIM(파일 무결성 모니터링)을 사용하는 방법에 대해 알아보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
* [보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항을 통해 Azure 리소스를 보호하는 방법을 알아봅니다.
* [보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 알아봅니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/)-- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
