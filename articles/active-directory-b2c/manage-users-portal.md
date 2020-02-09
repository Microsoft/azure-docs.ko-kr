---
title: Azure Portal에서 소비자 사용자 계정 Azure AD B2C 만들기 & 삭제
description: Azure Portal를 사용 하 여 Azure AD B2C 디렉터리에서 소비자 사용자를 만들고 삭제 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9d96db5cf062da1b2e8badd80a9a00620b724fd4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840420"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Azure Portal를 사용 하 여에서 소비자 사용자를 만들고 삭제 Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) 디렉터리에서 수동으로 소비자 계정을 만들려는 시나리오가 있을 수 있습니다. 사용자가 응용 프로그램 중 하나를 사용 하기 위해 등록할 때 Azure AD B2C 디렉터리의 소비자 계정은 가장 일반적으로 생성 되지만 Azure Portal를 사용 하 여 프로그래밍 방식으로 만들 수 있습니다. 이 문서에서는 사용자 만들기 및 삭제 Azure Portal 방법을 중점적으로 설명 합니다.

사용자를 추가 하거나 삭제 하려면 사용자의 계정에 *사용자 관리자* 또는 *전역 관리자* 역할이 할당 되어야 합니다.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>사용자 계정의 유형

[Azure AD B2C의 사용자 계정 개요](user-overview.md)에 설명 된 대로 Azure AD B2C 디렉터리에서 만들 수 있는 사용자 계정에는 다음 세 가지 유형이 있습니다.

* Work
* 게스트
* 소비자

이 문서에서는 Azure Portal에서 **소비자 계정** 으로 작업 하는 방법을 집중적으로 설명 합니다. 작업 및 게스트 계정을 만들고 삭제 하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자 추가 또는 삭제](../active-directory/fundamentals/add-users-azure-active-directory.md)를 참조 하세요.

## <a name="create-a-consumer-user"></a>소비자 사용자 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **새 사용자**를 선택합니다.
1. **사용자 Azure AD B2C 만들기**를 선택 합니다.
1. **로그인 방법을** 선택 하 고 새 사용자에 대한 **전자 메일** 주소 또는 **사용자 이름을** 입력 합니다. 여기에서 선택 하는 로그인 방법은 Azure AD B2C 테넌트의 *로컬 계정* id 공급자에 대해 지정한 설정과 일치 해야 합니다 (Azure AD B2C 테넌트에서 > **id 공급자** **관리** 참조).
1. 사용자의 **이름을** 입력 합니다. 일반적으로 사용자의 전체 이름 (지정 된 및 성)입니다.
1. 필드 사용자가 로그인 할 수 있는 기능을 지연 시키려는 경우 **로그인을 차단할** 수 있습니다. 나중에 Azure Portal에서 사용자의 **프로필** 을 편집 하 여 로그인을 사용 하도록 설정할 수 있습니다.
1. **암호 자동 생성** 을 선택 하거나 **암호를 만듭니다**.를 선택 합니다.
1. 사용자의 **이름과** **성을**지정 합니다.
1. **만들기**를 선택합니다.

**로그인 차단**을 선택 하지 않은 경우 사용자는 지정한 로그인 방법 (전자 메일 또는 사용자 이름)을 사용 하 여 로그인 할 수 있습니다.

## <a name="delete-a-consumer-user"></a>소비자 사용자 삭제

1. Azure AD B2C 디렉터리에서 **사용자**를 선택 하 고 삭제 하려는 사용자를 선택 합니다.
1. **삭제**를 선택한 다음 **예** 를 선택 하 여 삭제를 확인 합니다.

삭제 후 처음 30 일 이내에 사용자를 복원 하거나 사용자를 영구적으로 삭제 하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용 하 여 최근에 삭제 된 사용자 복원 또는 제거](../active-directory/fundamentals/active-directory-users-restore.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

자동화 된 사용자 관리 시나리오의 경우 (예: 다른 id 공급자에서 Azure AD B2C 디렉터리로 사용자 [마이그레이션) Azure AD B2C: 사용자 마이그레이션](user-migration.md)을 참조 하세요.
