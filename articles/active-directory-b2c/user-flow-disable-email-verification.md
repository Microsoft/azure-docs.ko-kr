---
title: 고객 등록 중에 전자 메일 확인 사용 안 함
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 고객 등록 중에 전자 메일 확인을 사용 하지 않도록 설정 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5b3598e33dac131c8881248a5f4b740a6302e20
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848019"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 고객 등록 중에 전자 메일 확인 사용 안 함

기본적으로 Azure Active Directory B2C (Azure AD B2C)는 로컬 계정 (메일 주소 또는 사용자 이름을 사용 하 여 등록 하는 사용자 계정)에 대해 고객의 전자 메일 주소를 확인 합니다. Azure AD B2C는 등록 프로세스 중에 고객이 해당 주소를 확인 하도록 요구 하 여 유효한 전자 메일 주소를 확인 합니다. 또한 악의적인 행위자가 자동화 된 프로세스를 사용 하 여 응용 프로그램에서 사기성 계정을 생성 하지 못하도록 방지 합니다.

일부 응용 프로그램 개발자는 등록 프로세스 중에 전자 메일 확인을 건너뛰고 나중에 고객이 전자 메일 주소를 확인 하는 것을 선호 합니다. 이를 지원하는 경우 전자 메일 확인을 사용하지 않도록 Azure AD B2C를 구성할 수 있습니다. 이렇게 하면 보다 원활한 등록 프로세스가 만들어지고 개발자에 게는 없는 고객의 전자 메일 주소를 확인 한 고객을 구분할 수 있는 유연성이 제공 됩니다.

전자 메일 확인을 사용 하지 않도록 설정 하려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 사용 하 여 Azure AD B2C 테넌트를 포함 하는 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **사용자 흐름**을 선택합니다.
1. 전자 메일 확인을 사용 하지 않도록 설정할 사용자 흐름을 선택 합니다. 예를 들어 *B2C_1_signinsignup*합니다.
1. **페이지 레이아웃**을 선택 합니다.
1. **로컬 계정 등록 페이지**를 선택 합니다.
1. **사용자 특성**에서 **전자 메일 주소**를 선택 합니다.
1. **확인 필요** 드롭다운 아래에서 **아니요**를 선택 합니다.
1. **저장**을 선택합니다. 이제이 사용자 흐름에 대해 메일 확인을 사용할 수 없습니다.

> [!WARNING]
> 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. 기본 Azure AD B2C 제공 전자 메일 확인을 사용 하지 않도록 설정 하는 경우 대체 확인 시스템을 구현 하는 것이 좋습니다.
