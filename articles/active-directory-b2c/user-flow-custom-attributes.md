---
title: Azure Active Directory B2C에서 사용자 지정 특성 정의 | Microsoft Docs
description: Azure Active Directory B2C에서 애플리케이션에 대한 사용자 지정 특성을 정의하여 고객에 대한 정보를 수집합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 433eff8f7ec22a3484e8e7f38dab2bb1c24e2fcc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848071"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 특성 정의

 모든 고객 대상 애플리케이션은 수집해야 하는 정보에 대한 특정 요구 사항이 있습니다. Azure Active Directory B2C (Azure AD B2C) 테넌트에는 지정 된 이름, 성, 구/군/시 및 우편 번호와 같은 특성에 저장 되는 기본 제공 정보 집합이 제공 됩니다. Azure AD B2C를 사용하면 각 고객 계정에 저장된 특성 집합을 확장할 수 있습니다.

 [Azure Portal](https://portal.azure.com/)에서 사용자 지정 특성을 만든 후 등록 사용자 흐름, 등록 또는 로그인 사용자 흐름 또는 프로필 편집 사용자 흐름에서 사용할 수 있습니다. 또한 [Azure AD Graph API](manage-user-accounts-graph-api.md)를 사용하여 이러한 특성을 읽고 쓸 수 있습니다. Azure AD B2C의 사용자 지정 특성은 [Azure AD Graph API 디렉터리 스키마 확장](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)을 사용합니다.

> [!NOTE]
> Azure AD B2C 테넌트 쿼리를 위한 최신 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) 에 대한 지원은 아직 개발 중입니다.
>

## <a name="create-a-custom-attribute"></a>사용자 지정 특성 만들기

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. 구독 정보를 선택한 다음, **디렉터리 전환**을 선택합니다.

    ![Azure AD B2C 테넌트로 전환](./media/user-flow-custom-attributes/switch-directories.png)

    테넌트를 포함하는 디렉터리를 선택합니다.

    ![디렉터리 및 구독 필터에 강조 표시 된 B2C 테넌트](./media/user-flow-custom-attributes/select-directory.PNG)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **사용자 특성**을 선택한 후 **추가**를 선택합니다.
5. 사용자 지정 특성(예: "ShoeSize")에 대해 **이름**을 제공합니다.
6. **데이터 형식**을 선택합니다. **문자열**, **부울** 및 **Int**만 사용할 수 있습니다.
7. 필요에 따라 정보 제공을 위해 **설명**을 입력합니다.
8. **만들기**를 클릭합니다.

이제 사용자 지정 특성을 **사용자 특성** 목록 및 사용자 흐름에서 사용할 수 있습니다. 사용자 지정 특성은 **사용자 특성** 목록에 추가될 때가 아니라 사용자 흐름에 사용될 때 처음으로 만들어집니다.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>사용자 흐름에 사용자 지정 특성 사용

1. Azure AD B2C 테넌트에서 **사용자 흐름**을 선택합니다.
2. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
4. **사용자 특성**을 선택하고 사용자 지정 특성을 선택합니다(예: "ShoeSize"). **저장**을 클릭합니다.
5. **애플리케이션 클레임**을 선택하고 사용자 지정 특성을 선택합니다.
6. **저장**을 클릭합니다.

새로 만든 사용자 지정 특성을 사용 하는 사용자 흐름을 사용 하 여 새 사용자를 만든 후에는 [AZURE AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)에서 개체를 쿼리할 수 있습니다. 또는 사용자 흐름에서 [**사용자 흐름 실행**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) 기능을 사용 하 여 고객 환경을 확인할 수 있습니다. 이제 등록 과정 동안 수집되는 특성 목록에 **ShoeSize**가 표시되며 애플리케이션으로 다시 전송되는 토큰에도 표시됩니다.

