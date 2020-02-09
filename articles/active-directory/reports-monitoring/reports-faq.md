---
title: Azure Active Directory 보고서 FAQ | Microsoft Docs
description: Azure Active Directory 보고서와 관련하여 자주 묻는 질문입니다.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbaf7d4e5a0dee82edf9b6b6bd65b91309e1344d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007687"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Azure Active Directory 보고서 관련 자주 묻는 질문

이 문서에는 Azure AD(Azure Active Directory) 보고에 대한 질문과 대답이 포함되어 있습니다. 자세한 내용은 [Azure Active Directory 보고](overview-reports.md)를 참조하세요. 

## <a name="getting-started"></a>시작 

**Q: 현재 `https://graph.windows.net/<tenant-name>/reports/` 엔드포인트 Api를 사용 하 여 Azure AD 감사 및 통합 응용 프로그램 사용 보고서를 프로그래밍 방식으로 보고 시스템으로 끌어옵니다. 어떻게 전환 해야 하나요?**

**A:** [API 참조](https://developer.microsoft.com/graph/)에서 [API를 사용하여 작업 보고서에 액세스하는 방법](concept-reporting-api.md)을 알아보세요. 이 엔드포인트에는 이전 API 엔드포인트에서 가져온 모든 데이터를 확인할 수 있는 두 개의 보고서(**감사** 및 **로그인**)가 있습니다. 이 새 엔드포인트에는 앱 사용, 디바이스 사용 및 사용자 로그인 정보를 가져오는 데 사용할 수 있는 Azure AD Premium 라이선스가 있는 로그인 보고서도 있습니다.

---

**Q: 현재 `https://graph.windows.net/<tenant-name>/reports/` 엔드포인트 Api를 사용 하 여 Azure AD 보안 보고서 (특정 유형의 검색 (예: 누출 된 자격 증명 또는 익명 IP 주소에서의 로그인)를 프로그래밍 방식으로 보고 시스템으로 끌어옵니다. 어떻게 전환 해야 하나요?**

**A:**  [Id 보호 위험 검색 API](../identity-protection/graph-get-started.md) 를 사용 하 여 Microsoft Graph를 통해 보안 검색에 액세스할 수 있습니다. 이 새로운 형식을 사용 하면 고급 필터링, 필드 선택 등을 통해 데이터를 쿼리 하는 방법에 더 많은 유연성을 제공 하 고, SIEMs 및 기타 데이터 수집 도구와 쉽게 통합 하기 위해 위험 검색을 한 가지 유형으로 표준화할 수 있습니다. 데이터가 다른 형식으로 되어 있으므로 이전 쿼리를 새 쿼리로 대체할 수 없습니다. 그러나 [새로운 API는 Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)를 사용합니다. 이것은 O365 또는 Azure AD와 같은 API에 대한 Microsoft 표준입니다. 따라서 필요한 작업이 현재 MS Graph 투자를 확장하거나 이러한 새 표준 플랫폼으로의 전환을 시작하도록 지원할 수 있습니다.

---

**Q: Premium 라이선스는 어떻게 받을 수 있나요?**

**A:** [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요.

---

**Q: Premium 라이선스를 받은 후 활동 데이터를 보는 데 얼마나 걸리나요?**

**A:** 기존에 무료 라이선스를 통해 수집한 활동 데이터가 있는 경우에는 해당 데이터를 즉시 볼 수 있습니다. 데이터가 없는 경우에는 보고서에 데이터가 나타나기까지 1~2일 정도 걸립니다.

---

**Q: Azure AD Premium 라이선스를 받은 후에 지난달 데이터를 볼 수 있나요?**

**A:** : 최근에 Premium 버전(평가판 버전을 포함)으로 전환한 경우 최대 7일간 데이터를 볼 수 있습니다. 데이터가 누적되면 지난 30일간의 데이터를 볼 수 있습니다.

---

**Q: Azure Portal의 활동 로그인을 보거나 API를 통해 데이터를 가져오려면 전역 관리자 권한이 필요한가요?**

**A:** 아니요. 테넌트의 **보안 읽기 권한자** 또는 **보안 관리자**도 포털 또는 API를 통해 보고 데이터에 액세스할 수 있습니다. **전역 관리자**도 물론 이 데이터에 접근할 수 있습니다.

---


## <a name="activity-logs"></a>활동 로그


**Q: Azure Portal에서 활동 로그 (감사 및 로그인)의 데이터 보존은 어떻게 됩니까?** 

**A:** 다음 표에는 활동 로그의 데이터 보존 기간이 나열되어 있습니다. 자세한 내용은 [Azure AD 보고서 데이터 보존 정책](reference-reports-data-retention.md)을 참조하세요.

| 보고서                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 감사 로그             | 7 일        | 30일             | 30일             |
| 로그인               | 해당 없음           | 30일             | 30일             |
| Azure MFA 사용        | 30일       | 30일             | 30일             |

---

**Q: 내 작업을 완료한 후 얼마나 지나야 활동 데이터를 볼 수 있나요?**

**A:** 감사 로그에는 15분에서 1시간까지의 대기 시간이 있습니다. 로그인 활동 로그는 레코드에 따라 15분에서 2시간까지 걸릴 수 있습니다.

---

**Q: Azure Portal을 통해 Office 365 활동 로그 정보를 얻을 수 있습니까?**

**A:** Office 365 활동 및 Azure AD 활동 로그가 많은 디렉터리 리소스를 공유 하는 경우에도 Office 365 활동 로그를 전체적으로 확인 하려면 [Microsoft 365 관리 센터](https://admin.microsoft.com) 로 이동 하 여 Office 365 활동 로그 정보를 얻어야 합니다.

---

**Q: Office 365 활동 로그에 대한 정보를 얻기 위해 어떤 API를 사용해야 합니까?**

**A:** [Office 365 관리 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)를 사용하여 API를 통해 Office 365 활동 로그에 액세스하세요.

---

**Q: Azure Portal에서 몇 개의 레코드를 다운로드할 수 있습니까?**

**A:** Azure Portal에서 최대 5,000개의 레코드를 다운로드할 수 있습니다. 레코드는 *가장 최근* 순으로 정렬되며, 기본적으로 가장 최근의 5,000개 레코드를 가져옵니다.

---

## <a name="risky-sign-ins"></a>위험한 로그인

**Q: Id 보호에 위험 검색이 있지만 로그인 보고서에 해당 로그인이 표시 되지 않습니다. 이것이 예상 되나요?**

**A:** 예. ID 보호는 대화형인지 여부에 관계없이 모든 인증 흐름의 위험을 평가합니다. 그러나 모든 로그인은 대화형 로그인만 표시합니다.

---

**Q: Azure Portal에서 로그인 또는 사용자에게 위험 플래그가 지정된 이유를 어떻게 알 수 있나요?**

**A:** **Azure AD Premium** 구독이 있는 경우 **위험 플래그가 지정 된 사용자** 를 선택 하거나 **위험한 로그인** 보고서에서 레코드를 선택 하 여 기본 위험 검색에 대해 자세히 알아볼 수 있습니다. **무료** 또는 **기본** 구독이 있는 경우 위험 및 위험한 로그인 보고서에서 사용자를 볼 수 있지만 기본 위험 검색 정보는 볼 수 없습니다.

---

**Q: IP 주소는 로그인 및 위험한 로그인 보고서에서 어떻게 계산되나요?**

**A:** IP 주소는 IP 주소와 해당 주소가 실제로 연결된 컴퓨터 간에 확실한 연결이 없는 경우와 같은 방법으로 발급됩니다. IP 주소 매핑은 클라이언트 디바이스가 실제로 사용되는 위치에서 종종 매우 먼 중앙 풀에서 IP 주소를 발급하는 VPN이나 모바일 공급자와 같은 요인들로 인해 더욱 복잡해집니다. 현재로서는 Azure AD 보고서에서 IP 주소를 실제 위치로 변환하는 것은 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보를 바탕으로 하는 최상의 노력입니다. 

---

**Q: "추가 위험이 있는 로그인"이 감지 되는 위험 검색은 무엇 인가요?**

**A:** 사용자 환경의 위험한 로그인을 모두 파악할 수 있도록, “추가 위험이 있는 로그인이 감지됨”은 Azure AD Identity Protection 구독자에게만 제공되는 감지에 대한 로그인의 자리 표시자로 기능합니다.

---

## <a name="conditional-access"></a>조건부 액세스

**Q: 이 기능의 새로운 점**

**A:** 이제 고객은 모든 로그인 보고서를 통해 조건부 액세스 정책의 문제를 해결할 수 있습니다. 고객은 조건부 액세스 상태를 검토 하 고 로그인에 적용 된 정책과 각 정책에 대 한 결과에 대 한 세부 정보를 확인할 수 있습니다.

**Q: 어떻게 시작하나요?**

**A:** 시작하기:

* [Azure Portal](https://portal.azure.com)에서 로그인으로 이동합니다.
* 문제를 해결할 로그인을 클릭합니다.
* **조건부 액세스** 탭으로 이동 합니다. 여기에서 로그인에 영향을 주는 모든 정책과 각 정책에 대 한 결과를 볼 수 있습니다. 
    
**Q: 조건부 액세스 상태에 대 한 가능한 모든 값은 무엇 인가요?**

**A:** 조건부 액세스 상태는 다음과 같은 값을 가질 수 있습니다.

* **적용되지 않음**: 범위 내 사용자 및 앱에 CA 정책이 없음을 의미합니다. 
* **성공**: 범위의 사용자 및 앱에 CA 정책이 있으며 CA 정책이 충족되었음을 의미합니다. 
* **실패**: 범위의 사용자 및 앱에 CA 정책이 있으며 CA 정책이 충족되지 않았음을 의미합니다. 
    
**Q: 조건부 액세스 정책 결과에 사용할 수 있는 모든 값은 무엇 인가요?**

**A:** 조건부 액세스 정책의 결과는 다음과 같습니다.

* **성공**: 정책이 충족되었습니다.
* **오류**: 정책이 충족되지 않았습니다.
* **적용되지 않음**: 정책 조건이 충족되지 않았기 때문일 수 있습니다.
* **사용 안 함**: 정책이 사용 안 함 상태이기 때문입니다. 
    
**Q: 모든 로그인 보고서의 정책 이름이 CA의 정책 이름과 일치 하지 않습니다. 굳이?**

**A:** 모든 로그인 보고서의 정책 이름은 로그인 시점의 CA 정책 이름을 기준으로 합니다. 나중, 즉 로그인 후에 정책 이름을 업데이트했다면 이 값이 CA의 정책 이름과 일관되지 않을 수 있습니다.

**Q: 조건부 액세스 정책으로 인해 로그인이 차단 되었지만 로그인 활동 보고서에 로그인이 성공 했음을 표시 합니다. 굳이?**

**A:** 현재 로그인 보고서는 조건부 액세스가 적용 될 때 Exchange ActiveSync 시나리오에 대 한 정확한 결과를 표시 하지 않을 수 있습니다. 보고서의 로그인 결과에 성공한 로그인이 표시 되는 경우에는 조건부 액세스 정책으로 인해 실제로 로그인에 실패 하는 경우가 있을 수 있습니다. 
