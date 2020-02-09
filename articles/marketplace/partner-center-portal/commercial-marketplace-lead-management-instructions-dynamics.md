---
title: 고객 Engagement의 Dynamics 365에 대 한 리드 관리 | Azure Marketplace
description: 고객 Engagement의 Dynamics 365에 대 한 리드 관리를 구성 합니다.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 37cf613b6e0bd2ec9910dd3e7431c0feaa02431c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812302"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>고객 Engagement의 Dynamics 365에 대 한 리드 관리 구성

이 문서에서는 고객 참여를 위한 Dynamics 365을 설정 하는 방법을 설명 합니다 (이전에 Dynamics CRM Online). marketplace 제품에서 판매 잠재 고객을 처리 하려면 [여기](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) 에서 변경 내용에 대해 자세히 알아보세요. 

>[!Note]
>이러한 지침은 고객 참여 환경용 Microsoft 호스트 된 클라우드 Dynamics 365에 적용 됩니다. Dynamics 온-프레미스 환경에 직접 연결 하는 것은 현재 지원 되지 않으며, 잠재 고객을 받기 위해 [https 엔드포인트](./commercial-marketplace-lead-management-instructions-https.md) 또는 [Azure 테이블](./commercial-marketplace-lead-management-instructions-azure-table.md) 을 구성 하는 등의 기타 옵션을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료 하려면 다음 사용자 권한이 필요 합니다.

* 솔루션을 설치 하 고 다음 지침을 따르려면 Dynamics 365에 대 한 관리자 여야 합니다.
* Marketplace 제품에서 잠재 고객을 보내는 데 사용 되는 잠재 고객 서비스에 대 한 새 서비스 계정을 만들려면 테넌트 관리자 여야 합니다.
* Office 365 관리 포털에 대 한 액세스 권한이 있어야 합니다.
* Azure Portal에 대 한 액세스 권한이 있어야 합니다.

## <a name="install-the-solution"></a>솔루션 설치

1.  [Microsoft Marketplace 리드 기록기 솔루션](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) 을 다운로드 하 고 컴퓨터에 로컬로 저장 합니다.

2.  Dynamics 인스턴스의 URL (예: `https://tenant.crm.dynamics.com`)으로 이동 하 여 고객 Engagement에 대 한 Dynamics 365을 엽니다.

3.  위쪽 탐색 모음에서 기어 아이콘 및 **고급 설정** 을 선택 하 여 설정에 액세스 합니다.
 
    ![Dynamics-고급 설정](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  설정 페이지에서 위쪽 탐색 모음에 있는 설정 메뉴에 액세스 하 고 **솔루션**을 선택 합니다.

    >[!Note]
    >다음 화면 캡처에 옵션이 표시 되지 않으면 계속 하는 데 필요한 권한이 없습니다. Dynamics 365에 대 한 관리자에 게 연락 하 여 고객 참여 인스턴스

    ![Dynamics 365-솔루션](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. 솔루션 페이지에서 **가져오기** 를 선택 하 고 1 단계에서 다운로드 한 *Microsoft Marketplace 리드 기록기* 솔루션을 저장 한 위치로 이동 합니다.

    ![고객 참여-가져오기에 대 한 Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. 솔루션 가져오기 마법사를 따라 솔루션 가져오기를 완료 합니다.

## <a name="configure-user-permissions"></a>사용자 권한 구성

고객 관계 인스턴스에 대 한 Dynamics 365에 잠재 고객을 기록 하려면 서비스 계정을 microsoft와 공유 하 고 계정에 대 한 사용 권한을 구성 해야 합니다.

다음 단계를 사용하여 서비스 계정을 만들고 사용 권한을 할당합니다. **Azure Active Directory** 또는 **Office 365**를 사용할 수 있습니다.

>[!Note]
>선택한 인증 옵션에 따라 선택한 내용에 따라 해당 지침으로 건너뛸 수 있습니다. [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) 또는 [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)을 참조 하세요.

### <a name="azure-active-directory"></a>Azure Active Directory

리드를 계속 받을 수 있도록 사용자 이름/암호를 업데이트 하지 않아도 되는 추가 혜택을 받을 수 있으므로이 옵션을 권장 합니다. Azure Active Directory 옵션을 사용 하려면 Active Directory 응용 프로그램에서 앱 ID, 응용 프로그램 키 및 디렉터리 ID를 제공 합니다.

다음 단계를 사용 하 여 고객 Engagement의 Dynamics 365에 대 한 Azure Active Directory를 구성 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 탐색 모음에서 Azure Active Directory 서비스를 선택 합니다.

2. Azure Active Directory 왼쪽 탐색에서 **속성** 을 선택 하 고 해당 페이지의 **디렉터리 ID** 값을 복사 합니다. Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에서 제공 해야 하는 *디렉터리 ID* 값 이므로이 값을 저장 합니다.

    ![Azure Active Directory-속성](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Azure Active Directory 왼쪽 탐색 영역에서 **앱 등록** 를 선택한 다음 해당 페이지에서 **새 등록** 을 선택 합니다.
4. 응용 프로그램 이름에 대 한 이름을 입력 합니다. 의미 있는 응용 프로그램 이름을 제공 합니다.
5. 지원 되는 계정 유형 아래에서 **조직 디렉터리의 계정**을 선택 합니다.
6. URI 리디렉션에서 **웹** 을 선택 하 고 uri (예: `https://contosoapp1/auth`)를 제공 합니다. 
7. **등록**을 선택합니다.

    ![애플리케이션 등록](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. 이제 응용 프로그램을 등록 했으므로 응용 프로그램의 개요 페이지에 액세스 하 고 해당 페이지에서 **응용 프로그램 (클라이언트) ID** 값을 복사 합니다. 이 값은 게시 포털에서 제공 해야 하는 *응용 프로그램 (클라이언트) ID* 값 이며 marketplace 제품에 대 한 잠재 고객을 받으려면이 값을 저장 합니다.

    ![응용 프로그램 (클라이언트) ID](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. 앱의 왼쪽 탐색 영역에서 **인증서 및 암호** 를 선택 하 고 해당 페이지에서 **새 클라이언트 암호** 를 선택 합니다. 클라이언트 암호에 대 한 의미 있는 설명을 입력 하 고 만료에서 **안 함** 옵션을 선택 합니다. **추가** 를 선택 하 여 클라이언트 암호를 만듭니다.

    ![응용 프로그램-인증 및 비밀](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. 클라이언트 암호를 성공적으로 만든 후에 **클라이언트 암호 값을 복사**합니다. 페이지에서 벗어나 이동한 후에는 값을 검색할 수 없습니다. Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에서 제공 해야 하는 *클라이언트 암호* 값 이므로이 값을 저장 합니다. 
11. 앱의 왼쪽 탐색 영역에서 **API 권한** 을 선택 하 고 **사용 권한 추가**를 선택 합니다.
12. Microsoft Api를 선택 하 고 **DYNAMICS CRM** 을 API로 선택 합니다.
13. *응용 프로그램에 필요한 사용 권한 유형에*따라 **위임 된 권한** 이 선택 되어 있는지 확인 합니다. *조직 사용자로 Common Data Service* **user_impersonation** 액세스 권한을 확인 하십시오. **권한 추가**를 선택 합니다.

    ![권한 추가](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Azure Portal에서 1-13 단계를 완료 한 후에는 URL (예: `https://tenant.crm.dynamics.com`)으로 이동 하 여 고객 Engagement 인스턴스의 Dynamics 365로 이동 합니다.
15. 위쪽 탐색 모음에서 기어 아이콘 및 **고급 설정** 을 선택 하 여 설정에 액세스 합니다.
16. 설정 페이지에서 위쪽 탐색 모음에 있는 설정 메뉴에 액세스 하 고 **보안**을 선택 합니다.
17. 보안 페이지에서 **사용자**를 선택 합니다.  사용자 페이지에서 "사용 사용자" 드롭다운을 선택 하 여 **응용 프로그램 사용자**로 전환 합니다.
18. 새 사용자를 만들려면 **새로 만들기**를 선택합니다. 

    ![새 사용자 만들기](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. **새 사용자**에서 사용자: 응용 프로그램 사용자가 선택 되어 있는지 확인 합니다. 이 연결에 사용 하려는 사용자의 사용자 이름, 전체 이름 및 전자 메일 주소를 제공 합니다. 또한 8 단계에서 만든 Azure Portal에서 만든 앱의 **응용 프로그램 ID** 를 붙여 넣습니다. **저장 후 닫기** 를 선택 하 여 사용자 추가를 완료 합니다.

    ![새 사용자](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. 이 사용자의 연결 구성을 완료하려면 이 문서의 “보안 설정”으로 이동합니다.

### <a name="office-365"></a>Office 365

Azure Active Directory 사용 하지 않으려는 경우 *Microsoft 365 관리 센터*에서 새 사용자를 등록할 수 있습니다. 잠재 고객을 계속 가져오려면 90일마다 사용자 이름/암호를 업데이트해야 합니다.

다음 단계를 사용 하 여 고객 참여를 위한 Dynamics 365 용 Office 365를 구성 합니다.

1. [Microsoft 365 관리 센터](https://admin.microsoft.com)에 로그인 합니다.

2. **사용자 추가**를 선택합니다.

    ![Microsoft 365 관리 센터-사용자 추가](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 리드 기록기 서비스에 대한 새 사용자를 만듭니다. 다음 설정을 구성합니다.

    * 사용자 이름 제공
    * 암호를 입력 하 고 "처음 로그인 할 때이 사용자가 암호를 변경 하도록 설정" 옵션의 선택을 취소 합니다.
    * 사용자 역할로 “사용자(관리자 액세스 권한 없음)”를 선택합니다.
    * 다음 화면 캡처에 표시 된 제품 라이선스로 "Dynamics 365 고객 참여 요금제"를 선택 합니다. 선택한 라이선스에 대한 요금이 청구됩니다. 

Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에서 제공 해야 하는 *사용자 이름 및 암호* 값으로 이러한 값을 저장 합니다.

![Microsoft 365 관리 센터-새 사용자](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>보안 설정

최종 단계는 만든 사용자가 잠재 고객을 기록할 수 있도록 하는 것입니다.

1. Dynamics 인스턴스의 URL (예: `https://tenant.crm.dynamics.com`)으로 이동 하 여 고객 Engagement에 대 한 Dynamics 365을 엽니다.
2. 위쪽 탐색 모음에서 기어 아이콘 및 **고급 설정** 을 선택 하 여 설정에 액세스 합니다.
3. 설정 페이지에서 위쪽 탐색 모음에 있는 설정 메뉴에 액세스 하 고 **보안**을 선택 합니다.
4. 보안 페이지에서 **사용자** 를 선택 하 고이 문서의 사용자 권한 구성 섹션에서 만든 사용자를 선택한 후 **역할 관리**를 선택 합니다. 

    ![역할 관리](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. 역할 이름 "Microsoft Marketplace 리드 작성기"를 검색 하 고 선택 하 여 사용자에 게 역할을 할당 합니다.

    ![사용자 역할 관리](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >이 역할은 사용자가 가져온 솔루션에서 생성되며 잠재 고객을 기록하고 호환성을 위해 솔루션 버전을 추적하는 권한만 갖습니다.

6. 보안 페이지로 다시 이동 하 고 **보안 역할**을 선택 합니다. "Microsoft Marketplace 리드 작성기" 역할을 검색 하 고 선택 합니다.

    ![보안 역할](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. 보안 역할에서 **핵심 레코드** 탭을 선택 합니다. "사용자 엔터티 UI 설정" 엔터티를 검색 하 고 해당 하는 각 원을 클릭 하 여 해당 엔터티에 대 한 사용자 (1/4 노란색 원)에 대 한 만들기, 읽기 및 쓰기 권한을 설정 합니다.

    ![Microsoft Marketplace 리드 작성기-코어 레코드](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 이제 [ **사용자 지정** ] 탭으로 이동 합니다. "시스템 작업" 엔터티를 검색 하 고 해당 하는 각 원에서 4 번 클릭 하 여 해당 엔터티에 대 한 읽기, 쓰기 및 appendto (solid green)를 사용 하도록 설정 합니다.

    ![Microsoft Marketplace 리드 작성기-사용자 지정](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **저장 하 고 닫습니다**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>고객 참여를 위한 Dynamics 365에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설치** 페이지로 이동 합니다.
2. 리드 관리 섹션에서 **연결** 을 선택 합니다.

    ![리드 관리에 연결](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. 연결 정보 팝업 창에서 **고객 Engagement에 대해 Dynamics 365** 을 선택 하 여 잠재 고객을 대상으로 합니다.

    ![연결 정보-리드 대상](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. `https://contoso.crm4.dynamics.com`와 같은 **Dynamics 365 인스턴스 URL** 을 제공 합니다.
5. **인증**, Azure Active Directory 또는 Office 365의 방법을 선택 합니다. 
6. Azure Active Directory를 선택한 경우 **응용 프로그램 (클라이언트) id** (예: `23456052-aaaa-bbbb-8662-1234df56788f`), **디렉터리 id** (예: `12345678-8af1-4asf-1234-12234d01db47`) 및 **클라이언트 암호** (예: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`)를 제공 합니다.

    ![연결 정보-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Office 365을 선택한 경우 **사용자 이름** (예: `contoso@contoso.onmicrosoft.com`) 및 암호를 제공 합니다 (예: `P@ssw0rd`).

    ![연결 정보-사용자 이름](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.
