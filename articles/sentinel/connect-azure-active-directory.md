---
title: Azure 센티널에 Azure Active Directory 데이터 연결 | Microsoft Docs
description: Azure Active Directory에서 데이터를 수집 하 고 Azure AD 로그인 로그 및 감사 로그를 Azure 센티널로 스트리밍하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 279f54c3de964580cc37d1288a6e1b7726348e10
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208625"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Azure Active Directory에서 데이터 연결 (Azure AD)



Azure 센티널의 기본 제공 커넥터를 사용 하 여 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 에서 데이터를 수집 하 고 azure 센티널로 스트리밍할 수 있습니다. 커넥터를 사용 하 여 [로그인 로그](../active-directory/reports-monitoring/concept-sign-ins.md) 및 [감사 로그](../active-directory/reports-monitoring/concept-audit-logs.md)를 스트리밍할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소


- Azure AD 라이선스 (Free/O365/P1/P2)는 모두 Azure 센티널로 로그인 로그를 수집 하는 데 충분 합니다. Azure Monitor (Log Analytics) 및 Azure 센티널에 대해 추가 기가바이트 요금이 적용 될 수 있습니다.

- 작업 영역에서 사용자에 게 Azure 센티널 참가자 역할을 할당 해야 합니다.

- 사용자에 게 로그를 스트리밍할 테 넌 트의 전역 관리자 또는 보안 관리자 역할이 할당 되어야 합니다.

- 연결 상태를 볼 수 있으려면 사용자에 게 Azure AD 진단 설정에 대 한 읽기 및 쓰기 권한이 있어야 합니다. 


## <a name="connect-to-azure-active-directory"></a>Azure Active Directory에 연결

1. Azure 센티널의 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure Active Directory** 을 선택한 다음 **커넥터 페이지 열기**를 선택 합니다.

1. Azure 센티널로 스트리밍할 로그 옆의 확인란을 표시 하 고 **연결**을 클릭 합니다.

1. Azure AD의 경고가 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택 하 여 연결 된 보안 서비스에 생성 된 경고에서 인시던트를 자동으로 생성 하는 기본 분석 규칙을 사용 하도록 설정 합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

1. Log Analytics에서 관련 스키마를 사용 하 여 Azure AD 경고를 쿼리하려면 `SigninLogs` `AuditLogs` 쿼리 창에 또는을 입력 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Active Directory를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
