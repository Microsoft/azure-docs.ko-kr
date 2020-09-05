---
title: Azure AD 조직(테넌트) 삭제 - Azure Active Directory | Microsoft Docs
description: 셀프 서비스 조직을 포함하여 Azure AD 조직(테넌트)을 삭제하도록 준비하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 05/21/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c0b203647bc57c7c7eb48e321895cf3b3fa7d44
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795425"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Azure Active Directory에서 테넌트 삭제

Azure AD 조직(테넌트)을 삭제하면 해당 조직에 포함된 모든 리소스도 삭제됩니다. 삭제하기 전에 연결된 리소스를 최소화하여 조직을 준비합니다. Azure AD(Azure Active Directory) 전역 관리자만 포털에서 Azure AD 조직을 삭제할 수 있습니다.

## <a name="prepare-the-organization"></a>조직 준비

Azure AD의 조직은 여러 가지 검사를 통과한 다음에 삭제할 수 있습니다. 이러한 검사는 Azure AD 조직을 삭제했을 때 사용자 액세스(예: Office 365에 로그인하거나 Azure의 리소스에 액세스하는 기능)에 부정적인 영향을 주는 위험을 줄일 수 있습니다. 예를 들어 구독과 연결된 조직이 의도치 않게 삭제된 경우 사용자는 해당 구독에 대한 Azure 리소스에 액세스할 수 없습니다. 다음과 같은 조건을 확인합니다.

* Azure AD 조직(테넌트)에는 조직을 삭제할 전역 관리자 한 명을 제외하고 사용자가 없을 수 있습니다. 조직을 삭제하려면 먼저 다른 모든 사용자를 삭제해야 합니다. 사용자가 온-프레미스에서 동기화된 경우 먼저 동기화를 해제해야 하며 Azure Portal 또는 Azure PowerShell cmdlet을 사용하여 클라우드 조직에서 사용자를 삭제해야 합니다.
* 조직에는 애플리케이션이 없을 수 있습니다. 조직을 삭제하려면 먼저 모든 애플리케이션을 제거해야 합니다.
* 조직에 연결된 다단계 인증 공급자가 없을 수 있습니다.
* Microsoft Azure, Office 365 또는 Azure AD Premium 등 Microsoft Online Services에 대한 구독이 조직과 연결되어 있지 않을 수 있습니다. 예를 들어 Azure에서 생성된 기본 Azure AD 조직을 Azure 구독에서 인증에 계속 사용하는 경우 이 조직을 삭제할 수 없습니다. 마찬가지로 다른 사용자가 구독을 연결한 조직은 삭제할 수 없습니다.

## <a name="delete-the-organization"></a>조직 삭제

1. 조직의 전역 관리자 계정을 사용하여 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택합니다.

3. 삭제하려는 조직으로 전환합니다.
  
   ![삭제 전 조직 확인](./media/directory-delete-howto/delete-directory-command.png)

4. **테넌트 삭제**를 선택합니다.
  
   ![조직을 삭제하는 명령을 선택합니다.](./media/directory-delete-howto/delete-directory-list.png)

5. 조직이 하나 이상의 검사를 통과하지 못하는 경우 통과 방법에 대한 자세한 정보가 포함된 링크가 제공됩니다. 모든 검사를 통과한 후 **삭제**를 선택하여 프로세스를 완료합니다.

## <a name="if-you-cant-delete-the-organization"></a>조직을 삭제할 수 없는 경우

Azure AD 조직을 구성한 경우 조직에 대해 Azure AD Premium P2, Office 365 Business Premium 또는 Enterprise Mobility + Security E5와 같은 라이선스 기반 구독을 활성화했을 수도 있습니다. 실수로 인한 데이터 손실을 방지하기 위해 구독이 완전히 삭제될 때까지 조직을 삭제할 수 없습니다. 조직 삭제를 허용하려면 구독이 **프로비저닝 해제됨** 상태여야 합니다. **만료됨** 또는 **취소됨** 상태의 구독은 **사용 안 함** 상태로 이동되며 최종 단계는 **프로비저닝 해제됨** 상태입니다.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)를 참조하세요. 

구독 상태 | 데이터 | 데이터 액세스
----- | ----- | -----
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자는 Microsoft 365 관리 센터 및 리소스에 대한 일반 액세스 권한이 있음 
만료됨(30일) | 모두 데이터에 액세스할 수 있음| 사용자는 Office 365 파일 또는 앱에 대한 일반 액세스 권한이 있음<br>관리자는 Microsoft 365 관리 센터 및 리소스에 대한 일반 액세스 권한이 있음
사용 안 함(30일) | 관리자만 데이터에 액세스할 수 있음 | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자는 Microsoft 365 관리 센터에 액세스할 수 있지만 사용자에게 라이선스를 할당하거나 사용자를 업데이트할 수 없음
프로비전 해제됨(사용 안 함으로 설정된 후 30일) | 데이터가 삭제됨(사용 중인 다른 서비스가 없는 경우 자동으로 삭제됨) | 사용자가 Office 365 파일 또는 앱에 액세스할 수 없음<br>관리자는 Microsoft 365 관리 센터에 액세스하여 다른 구독을 구매하고 관리할 수 있음

## <a name="delete-a-subscription"></a>구독 삭제

Microsoft 365 관리 센터를 통해 구독을 **프로비저닝 해제됨** 상태로 전환하여 3일 후에 삭제되도록 할 수 있습니다.

1. 조직의 전역 관리자 계정을 사용하여 [Microsoft 365 관리 센터](https://admin.microsoft.com)에 로그인합니다. 초기 기본 도메인인 contoso.onmicrosoft.com이 있는 “Contoso” 조직을 삭제하려는 경우 admin@contoso.onmicrosoft.com과 같은 UPN으로 로그인합니다.

2. **새 관리 센터 시도** 토글을 사용하도록 설정하여 새 Microsoft 365 관리 센터를 미리 봅니다.

   ![새 M365 관리 센터 환경 미리 보기](./media/directory-delete-howto/preview-toggle.png)

3. 새 관리 센터를 사용하도록 설정한 후에는 구독을 취소해야 삭제할 수 있습니다. **청구**를 선택하고 **제품 및 서비스**를 선택한 다음, 취소하려는 구독에 대해 **구독 취소**를 선택합니다. 피드백 페이지가 표시됩니다.

   ![취소할 구독 선택](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 피드백 양식을 작성하고 **구독 취소**를 선택하여 구독을 취소합니다.

   ![구독 미리 보기의 취소 명령](./media/directory-delete-howto/cancel-command.png)

5. 이제 구독을 삭제할 수 있습니다. 삭제하려는 구독에 대해 **삭제**를 선택합니다. **제품 및 서비스** 페이지에서 구독을 찾을 수 없는 경우 **구독 상태**가 **모두**로 설정되어 있는지 확인합니다.

   ![구독 삭제를 위한 삭제 링크](./media/directory-delete-howto/delete-command.png)

6. **구독 삭제**를 선택하여 구독을 삭제하고 계약조건에 동의합니다. 모든 데이터는 3일 이내에 영구적으로 삭제됩니다. 마음이 바뀌면 3일 기간 중 [구독을 다시 활성화](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)할 수 있습니다.
  
   ![계약조건을 자세히 읽기](./media/directory-delete-howto/delete-terms.png)

7. 이제 구독 상태가 변경되었으므로 구독이 삭제되도록 표시됩니다. 구독은 72시간 후에 **프로비전 해제됨** 상태가 됩니다.

8. 조직에서 구독을 삭제한 후 72시간이 경과하면 Azure AD 관리 센터에 다시 로그인할 수 있으며, 조직 삭제를 차단하는 구독과 필요한 작업 없이 Azure AD 조직을 성공적으로 삭제할 수 있습니다.
  
   ![삭제 시 구독 검사 통과 화면](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>삭제를 차단하는 평가판 구독이 있음

개별 사용자가 Office 365를 통해 등록할 수 있는 [셀프 서비스 등록 제품](/office365/admin/misc/self-service-sign-up?view=o365-worldwide)(예: Microsoft Power BI, Rights Management Services, Microsoft Power Apps, Dynamics 365)이 있으며, 제품을 등록하면 Azure AD 조직에서 인증을 위한 게스트 사용자도 생성됩니다. 이러한 셀프 서비스 제품은 데이터 손실을 방지하기 위해 조직에서 제품이 완전히 삭제될 때까지 디렉터리 삭제를 차단합니다. 또한 사용자가 제품을 개별적으로 등록했거나 할당받았는지 여부와 관계없이 Azure AD 관리자만 삭제할 수 있습니다.

할당되는 방법에 따라 두 가지 유형의 셀프 서비스 등록 제품이 있습니다. 

* 조직 수준 할당: Azure AD 관리자는 전체 조직에 제품을 할당하고, 사용자는 개별적으로 라이선스를 받지 않은 경우에도 이 조직 수준 할당으로 서비스를 활발히 사용할 수 있습니다.
* 사용자 수준 할당: 셀프 서비스 등록 중 개별 사용자는 기본적으로 관리자 없이 자신에게 제품을 할당합니다. 조직이 관리자에 의해 관리되는 경우([비관리형 조직의 관리자 인수](domains-admin-takeover.md) 참조) 관리자는 셀프 서비스 등록 없이 사용자에게 제품을 직접 할당할 수 있습니다.  

셀프 서비스 등록 제품을 삭제하기 시작하면, 해당 작업으로 인해 데이터가 영구적으로 삭제되고 서비스에 대한 모든 사용자 액세스가 제거됩니다. 제품이 개별적으로 또는 조직 수준에서 할당된 모든 사용자는 로그인 또는 기존 데이터에 대한 액세스가 차단됩니다. [Microsoft Power BI 대시보드](/power-bi/service-export-to-pbix) 또는 [Rights Management Services 정책 구성](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)과 같은 셀프 서비스 등록 제품의 데이터 손실을 방지하려면 데이터를 다른 위치에 백업하고 저장해야 합니다.

현재 사용할 수 있는 셀프 서비스 등록 제품 및 서비스에 대한 자세한 내용은 [사용 가능한 셀프 서비스 프로그램](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)을 참조하세요.

평가판 Office 365 구독(유료 파트너/CSP, 기업계약 또는 볼륨 라이선스를 포함하지 않음)이 만료될 경우 예상되는 결과는 다음 표를 참조하세요. Office 365 데이터 보존 및 구독 수명 주기에 대한 자세한 내용은 [비즈니스용 Office 365 구독이 종료되면 내 데이터와 액세스 권한에 어떤 변화가 있나요?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)를 참조하세요.

제품 상태 | 데이터 | 데이터 액세스
------------- | ---- | --------------
활성(평가판의 경우 30일) | 모두 데이터에 액세스할 수 있음 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 대한 일반적인 액세스 권한이 있습니다.<br>관리자는 Microsoft 365 관리 센터 및 리소스에 대한 일반 액세스 권한이 있음
Deleted | 데이터를 삭제함 | 사용자는 셀프 서비스 등록 제품, 파일 또는 앱에 액세스할 수 없습니다.<br>관리자는 Microsoft 365 관리 센터에 액세스하여 다른 구독을 구매하고 관리할 수 있음

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure Portal에서 셀프 서비스 등록 제품을 삭제하려면 어떻게 해야 하나요?

Microsoft Power BI 또는 Azure Rights Management Services와 같은 셀프 서비스 등록 제품을 **삭제** 상태로 전환하여 Azure AD 포털에서 즉시 삭제되도록 할 수 있습니다.

1. 조직의 전역 관리자 계정을 사용하여 [Azure AD 관리 센터](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다. 초기 기본 도메인인 contoso.onmicrosoft.com이 있는 “Contoso” 조직을 삭제하려는 경우 admin@contoso.onmicrosoft.com과 같은 UPN으로 로그온합니다.

2. **라이선스**를 선택한 후 **셀프 서비스 등록 제품**을 선택합니다. 사용자 기반 구독에서 모든 셀프 서비스 등록 제품을 별도로 볼 수 있습니다. 영구적으로 삭제하려는 제품을 선택합니다. Microsoft Power BI의 예제는 다음과 같습니다.

    ![사용자 이름이 잘못 입력되었거나 사용자 이름을 찾을 수 없음](./media/directory-delete-howto/licenses-page.png)

3. **삭제**를 선택하여 제품을 삭제한 다음, 데이터가 영구적으로 즉시 삭제된다는 사용 약관에 동의합니다. 이 삭제 작업은 모든 사용자를 제거하고 제품에 대한 조직 액세스를 제거합니다. ‘예’를 클릭하여 삭제를 진행합니다.  

    ![사용자 이름이 잘못 입력되었거나 사용자 이름을 찾을 수 없음](./media/directory-delete-howto/delete-product.png)

4. **예**를 선택하면 셀프 서비스 제품 삭제가 시작됩니다. 진행 중인 삭제를 알려 주는 알림이 표시됩니다.  

    ![사용자 이름이 잘못 입력되었거나 사용자 이름을 찾을 수 없음](./media/directory-delete-howto/progress-message.png)

5. 이제 셀프 서비스 등록 제품 상태가 **삭제됨**으로 변경되었습니다. 페이지를 새로 고치면 **셀프 서비스 등록 제품** 페이지에서 제품이 제거되어 있습니다.  

    ![사용자 이름이 잘못 입력되었거나 사용자 이름을 찾을 수 없음](./media/directory-delete-howto/product-deleted.png)

6. 모든 제품을 삭제하면 Azure AD 관리 센터에 다시 로그인할 수 있으며 조직 삭제를 차단하는 제품과 필요한 작업 없이 Azure AD 조직을 성공적으로 삭제할 수 있습니다.

    ![사용자 이름이 잘못 입력되었거나 사용자 이름을 찾을 수 없음](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>다음 단계

[Azure Active Directory 설명서](../index.yml)