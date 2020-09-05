---
title: 사용자 지정 도메인 이름 추가 및 확인 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 도메인 이름 관리에 대한 관리 개념 및 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12193b469b6e1f014e6c948da7b2adb370e78d4b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795337"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 도메인 이름 관리

대다수 디렉터리 리소스 식별자에서 중요한 부분인 도메인 이름은 사용자의 경우 사용자 이름 또는 전자 메일 주소 부분이고 그룹의 경우 주소 부분이며, 애플리케이션의 경우 경우에 따라 앱 ID URI 부분입니다. Azure AD(Azure Active Directory)의 리소스는 리소스를 포함하는 디렉터리가 소유하는 도메인 이름을 포함할 수 있습니다. 전역 관리자만 Azure AD에서 도메인을 관리할 수 있습니다.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대한 주 도메인 이름 설정

디렉터리를 만든 경우 'contoso.onmicrosoft.com'과 같은 초기 도메인 이름은 주 도메인 이름이기도 합니다. 주 도메인은 새 사용자를 만들 때 새 사용자에 대한 기본 도메인 이름입니다. 주 도메인 이름을 설정하면 관리자에 대한 프로세스를 간소화하여 포털에 새 사용자를 만듭니다. 주 도메인 이름을 변경하려면:

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**를 선택합니다.
3. **사용자 지정 도메인 이름**을 선택합니다.
  
   ![사용자 관리 페이지 열기](./media/domains-manage/add-custom-domain.png)
4. 기본 도메인으로 사용할 도메인의 이름을 선택합니다.
5. **Make primary** 명령을 선택합니다. 메시지가 표시되면 선택을 확인합니다.
  
   ![도메인 이름을 기본으로 설정](./media/domains-manage/make-primary-domain.png)

디렉터리의 주 도메인 이름을 페더레이션되지 않은 확인된 사용자 지정 도메인으로 변경할 수 있습니다. 디렉터리의 주 도메인을 변경해도 기존 사용자의 사용자 이름은 변경되지 않습니다.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Azure AD 조직에 사용자 지정 도메인 이름 추가

관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 도메인을 온-프레미스 Active Directory와 페더레이션하도록 구성하려는 경우 각 디렉터리에서 최대 450개의 도메인 이름을 추가할 수 있습니다.

## <a name="add-subdomains-of-a-custom-domain"></a>사용자 지정 도메인의 하위 도메인 추가

디렉터리에 'europe.contoso.com'과 같은 세 번째 수준 도메인 이름을 추가하려면 먼저 contoso.com과 같은 두 번째 수준 도메인을 추가 및 확인해야 합니다. Azure AD에서 자동으로 하위 도메인을 확인합니다. 추가한 하위 도메인이 확인되었는지 보려면 브라우저에서 도메인 목록을 새로 고칩니다.

참고

Azure AD 테넌트에 contoso.com 도메인을 이미 추가한 경우 하위 도메인 europe.contoso.com을 두 번째 Azure AD 테넌트에 추가할 수도 있습니다. 하위 도메인을 추가할 때 DNS 호스팅 공급자에 TXT 레코드를 추가하라는 메시지가 표시됩니다.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>사용자 지정 도메인 이름의 DNS 등록 기관을 변경하는 경우 수행할 작업

DNS 등록 기관을 변경하는 경우 Azure AD에서 추가로 수행해야 하는 구성 작업은 없습니다. Azure AD에서 해당 도메인 이름을 중단 없이 계속 사용할 수 있습니다. Office 365, Intune 또는 Azure AD의 사용자 지정 도메인 이름을 사용하는 다른 서비스에서 사용자 지정 도메인 이름을 사용하는 경우 해당 서비스에 대한 설명서를 참조하세요.

## <a name="delete-a-custom-domain-name"></a>사용자 지정 도메인 이름 삭제

조직이 더 이상 해당 도메인 이름을 사용하지 않는 경우 또는 다른 Azure AD와 해당 도메인 이름을 사용해야 하는 경우, Azure AD에서 사용자 지정 도메인 이름을 삭제할 수 있습니다.

사용자 지정 도메인 이름을 삭제하려면 먼저 디렉터리에 도메인 이름을 사용하는 리소스가 없는지 확인해야 합니다. 다음의 경우 디렉터리에서 도메인 이름을 삭제할 수 없습니다.

* 임의 사용자가 도메인 이름을 포함하는 사용자 이름, 메일 주소 또는 프록시 주소 사용
* 임의 그룹이 도메인 이름을 포함하는 메일 주소 또는 프록시 주소 사용
* Azure AD의 임의 애플리케이션이 도메인 이름을 포함하는 앱 ID URI 사용

사용자 지정 도메인 이름을 삭제하려면 먼저 Azure AD 디렉터리에서 이러한 리소스를 변경 또는 삭제해야 합니다.

### <a name="forcedelete-option"></a>ForceDelete 옵션

[Azure AD 관리 센터](https://aad.portal.azure.com)에서, 또는 [Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta)를 사용하여 도메인 이름 **ForceDelete**를 수행할 수 있습니다. 이러한 옵션은 비동기 작업을 사용하여 모든 참조를 “user@contoso.com” 등의 사용자 지정 도메인 이름에서 “user@contoso.onmicrosoft.com” 등의 초기 기본 도메인 이름으로 업데이트합니다. 

Azure Portal에서 **ForceDelete**를 호출하려면 도메인 이름에 대한 참조가 1,000개 미만인지 확인해야 합니다. 또한 프로비저닝 서비스가 Exchange인 모든 참조는 [ Exchange 관리 센터](https://outlook.office365.com/ecp/)에서 업데이트하거나 제거해야 합니다. 여기에는 Exchange 메일 사용이 가능한 보안 그룹 및 배포 목록이 포함됩니다. 자세한 내용은 [메일 사용이 가능한 보안 그룹 제거](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups)를 참조하세요. 또한 다음 중 하나에 해당하는 경우에는 **ForceDelete** 작업이 정상적으로 실행되지 않습니다.

* Office 365 도메인 구독 서비스를 통해 도메인을 구매한 경우
* 다른 고객 조직을 대신하여 관리하는 파트너

**ForceDelete** 작업의 일부분으로 수행되는 작업은 다음과 같습니다.

* 사용자 지정 도메인 이름에 대한 참조를 초기 기본 도메인 이름으로 변경하여 사용자의 UPN, EmailAddress 및 ProxyAddress 이름을 바꿉니다.
* 사용자 지정 도메인 이름에 대한 참조를 초기 기본 도메인 이름으로 변경하여 그룹의 EmailAddress 이름을 바꿉니다.
* 사용자 지정 도메인 이름에 대한 참조를 초기 기본 도메인 이름으로 변경하여 애플리케이션의 identifierUris 이름을 바꿉니다.

다음과 같은 경우에는 오류가 반환됩니다.

* 이름을 바꿀 개체 수가 1,000개보다 많은 경우
* 이름을 바꿀 애플리케이션 중 하나가 다중 테넌트 앱인 경우

### <a name="frequently-asked-questions"></a>질문과 대답

**Q: 도메인 삭제가 실패하고 이 도메인 이름에 Exchange Mastered 그룹이 있다는 오류가 표시되는 이유는 무엇인가요?** <br>
**A:** 현재 메일 사용이 가능한 보안 그룹 및 배포 목록과 같은 특정 그룹은 Exchange에서 프로비전되므로 [EAC(Exchange 관리 센터)](https://outlook.office365.com/ecp/)에서 수동으로 정리해야 합니다. 사용자 지정 도메인 이름을 사용하는 느린 ProxyAddresses가 있을 수도 있으며, 이러한 항목은 다른 도메인 이름으로 수동 업데이트해야 합니다. 

**Q: admin\@contoso.com으로 로그인했지만 "contoso.com" 도메인 이름을 삭제할 수 없습니다.**<br>
**A:** 사용자 계정 이름에서 삭제하려는 사용자 지정 도메인 이름을 참조할 수는 없습니다. 전역 관리자 계정이 admin@contoso.onmicrosoft.com과 같이 초기 기본 도메인 이름(.onmicrosoft.com)을 사용하는지 확인하세요. admin@contoso.onmicrosoft.com 등의 다른 전역 관리자 계정이나 “fabrikam.com” 등의 다른 사용자 지정 도메인 이름(계정: admin@fabrikam.com)으로 로그인해야 합니다.

**Q: 도메인 삭제 단추를 클릭했는데 삭제 작업의 상태가 `In Progress`로 표시됩니다. 소요 시간 작업이 실패하는 경우에는 어떻게 되나요?**<br>
**A:** 도메인 삭제 작업은 도메인 이름에 대한 모든 참조의 이름이 바뀌는 비동기 백그라운드 작업이며 1~2분 이내에 완료됩니다. 도메인 삭제가 실패하면 다음 중 하나에 해당하지 않는지 확인하세요.

* 앱이 appIdentifierURI를 사용하여 도메인 이름에 구성되어 있음
* 메일 사용이 가능한 그룹이 사용자 지정 도메인 이름을 참조함
* 도메인 이름에 대한 참조가 1,000개보다 많음

위의 조건을 충족하지 않는 경우에는 참조를 수동으로 정리하고 도메인을 다시 삭제해 보세요.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>PowerShell 또는 Microsoft Graph API를 사용하여 도메인 이름 관리

Azure Active Directory의 도메인 이름에 대한 대부분의 관리 작업은 Microsoft PowerShell을 사용하거나 프로그래밍 방식으로 Microsoft Graph API를 사용하여 완료할 수도 있습니다.

* [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](/powershell/module/azuread/?view=azureadps-2.0#domains)
* [도메인 리소스 유형](/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>다음 단계

* [사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Azure AD의 사용자 지정 도메인 이름에 대해 Exchange 관리 센터에서 Exchange 메일 사용이 가능한 보안 그룹 제거](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups)
* [Microsoft Graph API를 통해 사용자 지정 도메인 이름 ForceDelete](/graph/api/domain-forcedelete?view=graph-rest-beta)