---
title: Azure Active Directory ID 보호 알림
description: 알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 291afbdd902c7563e47595132d56b354dab28a3a
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950394"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD ID 보호는 사용자 위험 및 위험 검색을 관리 하는 데 도움이 되는 두 가지 유형의 자동화 된 알림 전자 메일을 보냅니다.

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.

## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨**이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](./overview-identity-protection.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

이 경고에 대 한 구성을 사용 하 여 경고를 생성 하려는 사용자 위험 수준을 지정할 수 있습니다. 사용자의 위험 수준이 지정한 내용에 도달 하면 전자 메일이 생성 됩니다. 예를 들어 보통 사용자 위험에 대해 경고를 표시 하도록 정책을 설정 하 고 사용자 John의 사용자 위험 점수를 실시간 로그인 위험으로 인해 보통 위험으로 이동 하는 경우 사용자에 게 위험 감지 전자 메일이 표시 됩니다. 사용자에 게 사용자 위험 수준 계산이 지정 된 위험 수준 (이상)이 되도록 하는 후속 위험 검색이 있는 경우 사용자 위험 점수를 다시 계산할 때 위험 검색 된 전자 메일에 추가 사용자를 받게 됩니다. 예를 들어 사용자가 1 월 1 일에 보통 위험으로 이동 하면 설정이 보통 위험에 대해 경고로 설정 된 경우 전자 메일 알림을 받게 됩니다. 동일한 사용자가 1 월 5 일에도 보통 위험에 대 한 다른 위험을 감지 하 고 사용자 위험 점수가 다시 계산 되며 여전히 medium 인 경우에는 다른 전자 메일 알림을 받게 됩니다. 

그러나 위험 검색이 발생 한 시간 (사용자 위험 수준 변경)이 마지막 전자 메일이 전송 된 시간 보다 최신인 경우에만 추가 전자 메일 알림이 전송 됩니다. 예를 들어 사용자가 1 월 1 일 오전 5 시에 로그인 하 고 실시간 위험이 없습니다. 즉, 해당 로그인으로 인해 메일이 생성 되지 않습니다. 10 분 후 오전 5:10에 동일한 사용자가 다시 로그인 하 고 실시간 위험을 발생 시켜 사용자 위험 수준이 높음으로 이동 하 고 전자 메일을 보낼 수 있습니다. 그런 다음 오전 5:15에는 오프 라인 위험 처리로 인해 5 시에 변경 되는 원래 로그인에 대 한 오프 라인 위험 점수가 높은 위험으로 변경 됩니다. 첫 번째 로그인 시간이 이미 전자 메일 알림을 트리거한 두 번째 로그인 이기 때문에 위험 전자 메일에 대해 플래그가 지정 된 추가 사용자가 전송 되지 않습니다.

전자 메일의 오버 로드를 방지 하기 위해 5 초 이내에 위험 검색 된 전자 메일에 한 명의 사용자만 받게 됩니다. 즉, 여러 사용자가 동일한 5 초 동안 지정 된 위험 수준으로 이동 하는 경우 모든 사용자에 대 한 위험 수준의 변경을 나타내는 하나의 전자 메일을 집계 하 고 보냅니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>위험 검색 됨 경고에 대 한 사용자 구성

관리자로서 다음을 설정할 수 있습니다.

- **이 전자 메일의 생성을 트리거하는 사용자 위험 수준** -기본적으로 위험 수준은 "높음" 위험으로 설정 됩니다.
- **이 전자 메일의 받는 사람** - 기본적으로 받는 사람에는 모든 전역 관리자가 포함됩니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.
   - 필요에 따라 **추가 전자 메일을 추가 하 여 경고 알림을 받을** 수 있습니다 .이 기능은 미리 보기 이며 사용자 정의 된 사용자가 Azure Portal에서 링크 된 보고서를 볼 수 있는 적절 한 권한이 있어야 합니다.

**Azure portal** **Azure Active Directory**  >  **보안**  >  **id 보호**  >  **사용자가 위험 검색 됨 경고에**Azure Portal의 위험에 노출 된 사용자 메일을 구성 합니다.

## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

주간 다이제스트 전자 메일에는 새로운 위험 검색에 대 한 요약이 포함 되어 있습니다.  
다음을 포함합니다.

- 새로운 위험한 사용자가 탐지됨
- 실시간으로 감지 된 새 위험한 로그인
- ID 보호에서 관련된 보고서에 대한 링크

![주간 다이제스트 전자 메일](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

기본적으로 받는 사람은 모든 전역 관리자를 포함 합니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.

### <a name="configure-weekly-digest-email"></a>주별 다이제스트 전자 메일 구성

관리자는 주별 다이제스트 전자 메일 보내기 또는 끄기를 전환 하 고 전자 메일을 받도록 할당 된 사용자를 선택할 수 있습니다.

**Azure portal** **Azure Active Directory**  >  **보안**  >  **id 보호**  >  **주간 다이제스트**의 Azure Portal에서 주별 다이제스트 전자 메일을 구성 합니다.

## <a name="see-also"></a>참조

- [Azure Active Directory ID 보호](./overview-identity-protection.md)