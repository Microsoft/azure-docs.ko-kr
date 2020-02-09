---
title: 동기화된 Azure AD Connect Health 사용 | Microsoft Docs
description: Azure AD Connect 동기화를 모니터링하는 방법을 설명하는 Azure AD Connect Health 페이지입니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61490f75d12967f7f396d5f767f2d2e696474572
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897213"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하여 Azure AD Connect 동기화 모니터링
다음 문서는 Azure AD Connect Health와 함께 Azure AD Connect (동기화) 모니터링에 중점을 둡니다.  Azure AD Connect Health와 함께 AD FS 모니터링에 대한 내용은 [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)을 참조하세요. 또한 Azure AD Connect Health와 함께 Active Directory Domain Services를 모니터링하는 방법에 대한 정보는 [AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)을 참조하세요.

![동기화에 대한 Azure AD Connect Health](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>동기화에 대한 Azure AD Connect Health에 대한 경고
동기화에 대한 Azure AD Connect Health 경고 섹션은 활성 경고 목록을 제공합니다. 각 경고에는 관련 정보, 해결 단계 및 관련된 설명서 링크가 포함됩니다. 활성 또는 해결된 경고를 선택하면 추가 정보는 물론, 경고를 해결하기 위해 수행할 수 있는 단계와 추가 설명서 링크가 포함된 새 블레이드가 표시됩니다. 과거에 해결된 경고에 대한 기록 데이터도 볼 수 있습니다.

경고를 선택하면 추가 정보는 물론 경고를 해결하기 위해 수행할 수 있는 단계와 추가 설명서 링크가 제공됩니다.

![Azure AD Connect 동기화 오류](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>제한된 경고 평가
Azure AD Connect가 기본 구성을 사용하지 않으면(예: 특성 필터링이 기본 구성에서 사용자 지정 구성으로 변경된 경우) Azure AD Connect Health 에이전트가 Azure AD Connect와 관련된 오류 이벤트를 업로드하지 않습니다.

이로 인해 서비스의 경고 평가가 제한됩니다. Azure Portal에서 해당 서비스 아래에 이 조건을 나타내는 배너가 표시됩니다.

![동기화에 대한 Azure AD Connect Health](./media/how-to-connect-health-sync/banner.png)

"설정"을 클릭하고 Azure AD Connect Health 에이전트가 모든 오류 로그를 업로드할 수 있도록 허용하여 이를 변경할 수 있습니다.

![동기화에 대한 Azure AD Connect Health](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>동기화 정보
관리자는 Azure AD에 대한 동기화 변경에 걸리는 시간과 변화의 양에 대해 자주 알아보려고 합니다. 이 기능은 아래 그래프를 사용하여 시각화하는 간편한 방법을 제공합니다.   

* 동기화 작업의 대기 시간
* 개체 변경 추세

### <a name="sync-latency"></a>동기화 대기 시간
이 기능은 커넥터에 대한 동기화 작업(가져오기, 내보내기, 등)의 대기 시간을 그래픽 추세로 표시니다.  작업의 대기 시간(변경 사항이 대규모인 경우 적합)뿐만 아니라 대기 시간에 좀 더 조사가 필요한 이상을 감지하는 방법을 빠르고 쉽게 이해하는 방법을 제공합니다.

![동기화 대기 시간](./media/how-to-connect-health-sync/synclatency02.png)

기본적으로 Azure AD 커넥터에 대한 '내보내기' 작업의 대기 시간만 표시됩니다.  커넥터에 대한 더 많은 작업 또는 다른 커넥터에서 작업을 보려면 차트를 마우스 오른쪽 단추로 클릭하고, 차트 편집을 선택하거나 "대기 시간 차트 편집" 버튼을 클릭하고 특정 작업 및 커넥터를 선택합니다.

### <a name="sync-object-changes"></a>동기화 개체 변경 사항
이 기능은 평가되어 Azure AD로 내보내진 변경 횟수를 그래픽 추세로 표시합니다.  요즘 동기화 로그에서 이러한 정보를 수집하는 것은 어려운 일입니다.  차트를 통해 사용자의 환경에서 발생하는 변경 횟수를 보다 간단하게 모니터링하는 방법뿐만 아니라 발생하는 오류를 시각적으로 볼 수 있습니다.

![동기화 대기 시간](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>개체 수준 동기화 오류 보고서
이 기능은 Azure AD Connect를 사용하여 Windows Server AD와 Azure AD 간의 ID 데이터를 동기화할 때 발생할 수 있는 동기화 오류에 대한 보고서를 제공합니다.

* 보고서에서는 동기화 클라이언트를 통해 기록된 오류를 포함합니다(Azure AD Connect 1.1.281.0 버전 이상)
* 동기화 엔진의 마지막 동기화 작업에서 발생한 오류를 포함합니다 (Azure AD 커넥터에 “내보내기”).
* 동기화에 대한 Azure AD Connect Health agent에는 최신 데이터를 포함하는 보고서에 필요한 엔드포인트의 아웃바운드 연결이 있어야 합니다.
* 보고서는 동기화를 위해 Azure AD Connect Health 에이전트가 업로드 한 데이터를 사용 하 여 **30 분 마다 업데이트** 됩니다. 다음과 같은 주요 기능을 제공 합니다.

  * 오류 분류
  * 범주별 오류에 따른 개체의 목록
  * 한 곳에서 발생한 오류에 대한 모든 데이터
  * 충돌로 인해 오류와 함께 개체의 특징을 비교 정렬
  * CVS로 오류 보고서를 다운로드

### <a name="categorization-of-errors"></a>오류 분류
보고서에서는 기존 동기화 오류를 다음과 같은 범주로 분류합니다.

| 범주 | Description |
| --- | --- |
| 중복 특성 |proxyAddresses, UserPrincipalName 같은 테넌트 내에서 고유해야 하는 Azure AD에서 하나 이상의 특성의 값이 중복된 개체를 만들거나 업데이트하려고 시도할 때의 오류 |
| 데이터 불일치 |소프트 일치가 동기화 오류가 발생하는 개체와 일치하도록 하는 데 실패할 경우발생하는 오류 |
| 데이터 유효성 검사 실패 |UserPrincipalName와 같은 중요한 특성에서 지원되지 않는 문자 등 잘못된 데이터로 인한 오류, Azure AD에 기록되기 전에 유효성 검사에 실패하는 서식 오류. |
| 페더레이션된 도메인 변경 | 여러 계정에서 서로 다른 페더레이션된 도메인을 사용하는 경우 오류가 발생합니다. |
| 큰 특성 |하나 이상의 특성의 허용 되는 크기, 길이 또는 개수보다 클 때 발생하는 오류 |
| 기타 |위 범주에 맞지 않는 다른 모든 오류 의견에 따라 이 범주는 하위 범주로 분할됩니다. |

![동기화 오류 보고서 요약](./media/how-to-connect-health-sync/errorreport01.png)
![동기화 오류 보고서 범주](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>범주별 오류에 따른 개체의 목록
각 범주에 대해 자세히 알아보면 해당 범주의 오류가 포함된 개체의 목록을 제공합니다.
![동기화 오류 보고서 목록](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>오류 세부 정보
다음 데이터를 각 오류에 대한 자세한 보기에 사용할 수 있음

* 강조 표시된 충돌하는 특성
* 관련된 *AD 개체*에 대한 식별자
* 관련된 *Azure AD 개체*에 대한 식별자
* 오류 설명 및 해결 방법

![동기화 오류 보고서 세부 정보](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>CVS로 오류 보고서를 다운로드
“내보내기” 단추를 선택하면 모든 오류에 대한 세부 정보를 모두 포함하는 CSV 파일을 다운로드할 수 있습니다.

### <a name="diagnose-and-remediate-sync-errors"></a>동기화 오류 진단 및 수정 
사용자 원본 앵커 업데이트와 관련된 특정 중복된 특성 동기화 오류 시나리오의 경우 포털에서 직접 수정할 수 있습니다. [중복된 특성 동기화 오류 진단 및 수정](how-to-connect-health-diagnose-sync-errors.md)에 대해 자세한 내용 읽기

## <a name="related-links"></a>관련된 링크
* [동기화 중 오류 문제 해결](tshoot-connect-sync-errors.md)
* [중복 특성 복원력](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)
* [AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health 버전 내역](reference-connect-health-version-history.md)
