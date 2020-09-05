---
title: 애플리케이션에 사용자를 할당하는 방법 | Microsoft Docs
description: 테넌트의 애플리케이션에 사용자가 할당되는 방법 이해
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 516bffa7057f8fee3b8e38d46f3b2da905880044
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639939"
---
# <a name="how-to-assign-users-to-applications"></a>애플리케이션에 사용자를 할당하는 방법

이 문서에서는 테넌트의 애플리케이션에 사용자가 할당되는 방법을 이해하는 데 도움을 줍니다.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Azure AD의 애플리케이션에 사용자가 할당되는 방법

사용자는 애플리케이션에 액세스하려면 어떤 방식으로든 먼저 애플리케이션에 할당되어야 합니다. 할당은 관리자, 비즈니스 대리자, 경우에 따라 사용자 자신이 수행할 수 있습니다. 아래에서는 사용자가 애플리케이션에 할당될 수 있는 방법을 설명합니다.

1.  관리자가 애플리케이션에 직접 [사용자를 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

2.  관리자가 다음을 포함하여 해당 사용자가 애플리케이션의 구성원인 [그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

    * 온-프레미스에서 동기화된 그룹

    * 클라우드에서 만든 정적 보안 그룹

    * 클라우드에서 만든 [동적 보안 그룹](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

    * 클라우드에서 만든 Office 365 그룹

    * [모든 사용자](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) 그룹

3.  관리자는 [셀프 서비스 응용 프로그램 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 를 통해 사용자가 [내 앱](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 을 사용 하 여 응용 프로그램을 추가할 수 있습니다. **비즈니스 승인 없이** 앱 **추가** 기능을 사용 합니다.

4.  관리자는 [셀프 서비스 응용 프로그램 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 를 통해 사용자가 [내 앱](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **앱 추가** 기능을 사용 하 여 응용 프로그램을 추가할 수 있지만,**선택한 비즈니스 승인자에 게 사전 승인이 있는** 경우에만이 응용 프로그램을 추가할 수 있습니다.

5.  관리자가 [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)를 통해 사용자가 **비즈니스 승인 없이** 애플리케이션이 할당된 그룹에 가입하도록 허용

6.  관리자가 [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)를 통해 사용자가 **선택된 비즈니스 승인자 세트의 사전 승인이 있는 경우에만** 애플리케이션이 할당된 그룹에 가입하도록 허용

7.  관리자가 [Microsoft Office 365](https://products.office.com/)와 같은 자사 애플리케이션에 대해 직접, 사용자에게 라이선스 할당

8.  관리자가 해당 [Microsoft Office 365](https://products.office.com/)와 같은 자사 애플리케이션의 구성원인 그룹에 라이선스 할당

9.  [관리자가 모든 사용자가 사용할 수 있게 애플리케이션에 동의](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)한 다음, 사용자가 애플리케이션에 로그인

10. 사용자가 애플리케이션에 로그인하여 [애플리케이션에 직접 동의](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
