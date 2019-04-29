---
title: Dynamics CRM | Microsoft Docs
description: Dynamics CRM의 잠재 고객 관리 구성
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: a1398d172a5c578ec3c0f16627eadd1da3fd1e45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778093"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Dynamics CRM Online의 잠재 고객 관리 구성

이 문서에서는 판매 잠재 고객을 처리하도록 Dynamics CRM Online을 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 사용자 권한이 필요합니다.
- 솔루션을 설치하려면 Dynamics CRM Online 인스턴스의 관리자여야 합니다.
- 잠재 고객 서비스에 대한 새 서비스 계정을 만들려면 테넌트 관리자여야 합니다.

<a name="install-the-solution"></a>솔루션 설치
--------------------

1.  [Microsoft Marketplace 잠재 고객 기록기 솔루션](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)을 다운로드하여 로컬에 저장합니다.

2.  Dynamics CRM Online을 열고 설정으로 이동합니다.
    >[!NOTE]
    >다음 화면 캡처에 옵션이 표시되지 않으면 필요한 사용 권한이 없는 것입니다.
 
       ![Dynamics 설정 보기](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  **가져오기**를 선택하고, 1단계에서 다운로드한 솔루션을 선택합니다.
 
    ![Dynamics 가져오기 옵션](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  솔루션 설치를 완료합니다.

## <a name="configure-user-permissions"></a>사용자 권한 구성

Dynamics CRM 인스턴스에 잠재 고객을 기록하려면 서비스 계정을 Microsoft와 공유하고 계정의 사용 권한을 구성해야 합니다.

다음 단계를 사용하여 서비스 계정을 만들고 사용 권한을 할당합니다. **Azure Active Directory** 또는 **Office 365**를 사용할 수 있습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

잠재 고객을 계속 가져오기 위해 사용자 이름/암호를 업데이트할 필요가 없는 추가 혜택이 있기 때문에 이 옵션을 사용하는 것이 좋습니다. Azure Active Directory 옵션을 사용하려면 Active Directory 애플리케이션의 앱 ID, 애플리케이션 키 및 디렉터리 ID를 제공합니다.

Dynamics CRM에 대해 Azure Active Directory를 구성하려면 다음 단계를 사용합니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Active Directory 서비스를 선택합니다.

2.  **속성**을 선택하고 **디렉터리 ID**를 복사합니다. 이는 Cloud 파트너 포털에서 사용해야 하는 테넌트 계정 식별입니다.

    ![디렉터리 ID 가져오기](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  **앱 등록**을 선택하고 **새 애플리케이션 등록**을 선택합니다.
4.  애플리케이션 이름을 입력합니다.
5.  유형으로 **웹앱/API**를 선택합니다.
6.  URL을 제공합니다. 이 필드는 잠재 고객에는 필요하지 않지만 애플리케이션을 만드는 데 필요합니다.
7. **만들기**를 선택합니다.
8.  이제 애플리케이션이 등록되었으므로 **속성**을 선택하고 **애플리케이션 ID 복사**를 선택합니다. Cloud 파트너 포털에서 이 연결 정보를 사용합니다.
9.  속성에서 애플리케이션을 다중 테넌트로 설정하고 **저장**을 선택합니다.

10. **키**를 선택하고 지속 기간이 *사용 기간 제한 없음*으로 설정된 새 키를 만듭니다. **저장**을 선택하여 키를 만듭니다. 
11. 키 메뉴에서 **키 값 복사**를 선택합니다. Cloud 파트너 포털에 필요하므로 이 값의 복사본을 저장합니다.
    
    ![Dynamics에서 등록된 키 가져오기](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. **필요한 권한**을 선택하고 **추가**를 선택합니다. 
13. 새 API로 **Dynamics CRM Online**을 선택하고 *조직 사용자로 CRM Online 액세스* 권한을 선택합니다.

14. Dynamics CRM에서 사용자로 이동한 다음, “사용할 수 있는 사용자” 드롭다운을 선택하여 **애플리케이션 사용자**로 전환합니다.
    
    ![애플리케이션 사용자](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. 새 사용자를 만들려면 **새로 만들기**를 선택합니다. 선택 된 **사용자: 응용 프로그램 사용자** 드롭다운 합니다.
    
    ![새 애플리케이션 사용자 추가](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. **새 사용자**에서 이 연결에 사용할 이름과 메일을 입력합니다. Azure Portal에서 만든 앱의 **애플리케이션 ID**를 붙여넣습니다.

     ![새 사용자를 구성 합니다.](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. 이 사용자의 연결 구성을 완료하려면 이 문서의 “보안 설정”으로 이동합니다.

### <a name="office-365"></a>Office 365

Azure Active Directory를 사용 하지 않으려는 경우에 새 사용자를 등록할 수 있습니다 합니다 *Microsoft 365 관리 센터*합니다. 잠재 고객을 계속 가져오려면 90일마다 사용자 이름/암호를 업데이트해야 합니다.

Dynamics CRM에 대해 Office 365를 구성하려면 다음 단계를 사용합니다.

1. 에 로그인 합니다 [Microsoft 365 관리 센터](https://admin.microsoft.com)합니다.

2. 선택 된 **관리자** 바둑판식으로 배열 합니다.

    ![Office Online Admin](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. **사용자 추가**를 선택합니다.

    ![사용자 추가](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. 리드 기록기 서비스에 대한 새 사용자를 만듭니다. 다음 설정을 구성합니다.

    -   암호를 입력하고 “이 사용자가 처음으로 로그인하는 경우 암호를 변경하도록 요청” 옵션의 선택을 취소합니다.
    -   사용자 역할로 “사용자(관리자 액세스 권한 없음)”를 선택합니다.
    -   다음 화면 캡처에 표시된 제품 라이선스를 선택합니다. 선택한 라이선스에 대한 요금이 청구됩니다. 솔루션은 Dynamics CRM Online 기본 라이선스에서도 작동합니다.
    
    ![사용자 권한 및 라이선스를 구성 합니다.](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>보안 설정

최종 단계는 만든 사용자가 잠재 고객을 기록할 수 있도록 하는 것입니다.

1.  Dynamics CRM Online에 로그인합니다.
2.  **설정**에서 **보안**을 선택합니다.
    
    ![보안 설정](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  **사용자 권한**에서 만든 사용자를 선택하고 **사용자 역할 관리**를 선택합니다. **Microsoft Marketplace 잠재 고객 기록기**를 선택하여 역할을 할당합니다.

    ![사용자 역할 할당](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >이 역할은 사용자가 가져온 솔루션에서 생성되며 잠재 고객을 기록하고 호환성을 위해 솔루션 버전을 추적하는 권한만 갖습니다.

4.  보안에서 **보안 역할**을 선택하고 Microsoft Marketplace 잠재 고객 기록기의 역할을 찾습니다.
    
    ![보안 리드 기록기 구성](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. **핵심 레코드** 탭을 선택합니다. 사용자 엔터티 UI에 대해 만들기/읽기/쓰기를 사용하도록 설정합니다.

    ![사용자에 대해 만들기/읽기/쓰기 사용](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>마무리

생성된 계정 정보를 Cloud 파트너 포털에 추가하여 잠재 고객 관리용으로 Dynamics CRM 구성을 완료합니다. 예를 들면 다음과 같습니다.

-   **Azure Active Directory** - **Application-id** (예: *23456052-aaaa-bbbb-8662-1234df56788f*), **디렉터리 Id** (예: *12345678-8af1-4asf-1234-12234d01db47*), 및 **응용 프로그램 키** (예: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **Url** (예: *https://contoso.crm4.dynamics.com*), **사용자 이름을** (예: *contoso\@ contoso.onmicrosoft.com*), 및 **암호** (예: *P\@ssw0rd*).
