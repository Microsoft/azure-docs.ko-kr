---
title: 조건부 액세스를 사용 하는 앱 보호 정책-Azure Active Directory
description: Azure Active Directory에서 조건부 액세스를 사용 하 여 cloud app access에 대 한 앱 보호 정책을 요구 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c853ef3a5a40381aba4e1c13eaf9ad7d8653170
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186627"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>조건부 액세스를 사용 하 여 cloud app access에 앱 보호 정책 필요 (미리 보기)

직원은 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원이 생산적이 될 수 있도록 하는 반면 데이터 손실을 방지하려고 합니다. Azure AD (Azure Active Directory) 조건부 액세스를 사용 하면 클라우드 앱에 대 한 액세스를 제한 하 여 회사 데이터를 보호할 수 있습니다. 먼저 앱 보호 정책을 사용 하 여 클라이언트 앱을 사용 합니다.

이 문서에서는 데이터에 액세스 권한을 부여 하기 전에 앱 보호 정책을 요구할 수 있는 조건부 액세스 정책을 구성 하는 방법을 설명 합니다.

## <a name="overview"></a>개요

[AZURE AD 조건부 액세스](overview.md)를 사용 하 여 권한 있는 사용자가 리소스에 액세스 하는 방법을 미세 조정할 수 있습니다. 예를 들어 클라우드 앱에 대한 액세스를 신뢰할 수 있는 디바이스로 제한할 수 있습니다.

[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 회사의 데이터를 보호할 수 있습니다. Intune 앱 보호 정책에는 MDM (모바일 장치 관리) 솔루션이 필요 하지 않습니다. 장치 관리 솔루션에 장치를 등록 하거나 등록 하지 않고 회사 데이터를 보호할 수 있습니다.

조건부 액세스를 Azure Active Directory 하 여 Intune에서 앱 보호 정책을 수신 하는 것으로 Azure AD에 보고 한 클라이언트 응용 프로그램에 대 한 클라우드 앱 액세스를 제한 합니다. 예를 들어 Exchange Online에 대 한 액세스를 Intune 앱 보호 정책이 있는 Outlook 앱으로 제한할 수 있습니다.

조건부 액세스 용어로 이러한 클라이언트 앱은 *앱 보호 정책을*사용 하 여 보호 되는 정책으로 알려져 있습니다.  

![조건부 액세스](./media/app-protection-based-conditional-access/05.png)

정책으로 보호 된 클라이언트 앱의 목록은 [앱 보호 정책 요구 사항](concept-conditional-access-grant.md)을 참조 하세요.

앱 보호 기반 조건부 액세스 정책을 [장치 기반 조건부 액세스 정책과](require-managed-devices.md)같은 다른 정책과 결합할 수 있습니다. 이러한 방식으로 개인 및 회사 장치 모두에 대 한 데이터를 보호 하는 방법을 유연 하 게 제공할 수 있습니다.

> [!NOTE]
> 초대 하는 조직이 B2B 사용자의 홈 조직을 볼 수 없기 때문에 조건부 액세스 앱 보호 정책을 B2B 사용자에 게 적용할 수 없습니다.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>앱 보호 기반 조건부 액세스 요구 사항의 이점

Intune에서 관리 되는 장치에 대해 Intune에서 보고 하는 규정 준수와 마찬가지로, 이제 Intune은 앱 보호 정책이 적용 되는 경우 Azure AD에 보고 합니다. 조건부 액세스는이 정책을 액세스 확인으로 사용할 수 있습니다. 새 조건부 액세스 정책 인 앱 보호 정책을 사용 하면 보안이 강화 됩니다. 다음과 같은 관리 오류 로부터 보호 합니다.

- Intune 라이선스가 없는 사용자입니다.
- Intune 앱 보호 정책을 받을 수 없는 사용자입니다.
- 정책을 수신 하도록 구성 되지 않은 Intune 앱 보호 정책 앱입니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다.

- [앱 보호 정책 요구 사항](concept-conditional-access-grant.md)입니다.
- [승인 된 클라이언트 앱 요구 사항](concept-conditional-access-grant.md)입니다.
- [Azure Active Directory의 조건부 액세스](overview.md)에 대 한 기본 개념입니다.
- [조건부 액세스 정책을 구성](app-based-mfa.md)하는 방법

## <a name="prerequisites"></a>사전 요구 사항

앱 보호 기반 조건부 액세스 정책을 만들려면 다음을 수행 해야 합니다.

- Enterprise Mobility + Security 또는 Azure Active Directory premium subscription + Intune이 있어야 합니다.
- 사용자에 게 Enterprise Mobility + Security 또는 Azure AD + Intune에 대 한 라이선스가 있는지 확인 합니다.
- 앱 보호 정책을 받도록 클라이언트 앱이 Intune에 구성 되어 있는지 확인 합니다.
- Intune 앱 보호 정책을 수신 하도록 사용자가 Intune에 구성 되어 있는지 확인 합니다.

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 조건부 액세스 정책으로 구성 됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- Exchange에 연결 하기 위해 iOS 또는 Android에서 네이티브 메일 응용 프로그램을 사용 하 여 전자 메일을 구성 합니다.
- Outlook 앱을 사용 하는 경우에만 액세스를 사용할 수 있음을 나타내는 전자 메일을 받습니다.
- 링크를 사용 하 여 응용 프로그램을 다운로드 합니다.
- Outlook 응용 프로그램을 열고 Azure AD 자격 증명으로 로그인 합니다.
- 계속 하려면 **Microsoft Authenticator 앱** 을 설치 하 라는 메시지가 표시 되거나 **Intune 회사 포털** .
- 응용 프로그램을 설치 하 고 계속 하려면 Outlook 앱으로 돌아갑니다.
- 장치를 등록 하 라는 메시지가 표시 됩니다.
- Intune 앱 보호 정책을 받을 수 있습니다.
- 전자 메일에 액세스할 수 있습니다.

회사 데이터에 액세스 하려면 모든 Intune 앱 보호 정책이 응용 프로그램에 있어야 합니다. 정책에서 사용자에 게 응용 프로그램을 다시 시작 하거나 추가 PIN을 사용 하 라는 메시지가 표시 될 수 있습니다. 이는 응용 프로그램 및 플랫폼에 대해 정책이 구성 된 경우에 해당 합니다.

### <a name="configuration"></a>구성

**1 단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/01.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange Online**을 선택 합니다.

   ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

1. **조건**에서 **장치 플랫폼** 및 **클라이언트 앱 구성 (미리 보기)** :
   1. **장치 플랫폼**에서 **Android** 및 **iOS**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

   1. **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

1. **액세스 제어**에서 **앱 보호 정책 필요 (미리 보기)** 를 선택 합니다.

   ![조건부 액세스](./media/app-protection-based-conditional-access/05.png)

**2 단계: ActiveSync (EAS)를 사용 하 여 Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange Online**을 선택 합니다.

   ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

1. **조건**에서 **클라이언트 앱 (미리 보기)** 을 구성 합니다. 

   1. **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

   1. **액세스 제어**에서 **앱 보호 정책 필요 (미리 보기)** 를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/05.png)

**3 단계: iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune를 사용 하 여 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조 하세요.

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 또는 준수 장치 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 또는 규정 준수 장치 조건부 액세스 정책으로 구성 됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음을 가정합니다.
 
- 사용자가 이미 회사 장치를 사용 하거나 사용 하지 않고 등록 되어 있습니다.
- 앱 보호 응용 프로그램을 사용 하 여 Azure AD에 등록 되 고 등록 되지 않은 사용자는 리소스에 액세스 하기 위해 장치를 등록 해야 합니다.
- 앱 보호 응용 프로그램을 사용 하는 등록 된 사용자는 장치를 다시 등록할 필요가 없습니다.
- 등록 되지 않은 경우 사용자가 Intune 앱 보호 정책을 받을 수 있습니다.
- 등록 되지 않은 경우 사용자가 Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수 있습니다.
- 장치가 등록 된 경우 사용자가 Outlook으로 메일에 액세스할 수 있습니다.

### <a name="configuration"></a>구성

**1 단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/62.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange Online**을 선택 합니다. 

   ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

1. **조건**에서 **장치 플랫폼** 및 **클라이언트 앱 (미리 보기)** 을 구성 합니다. 
 
   1. **장치 플랫폼**에서 **Android** 및 **iOS**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

   1. **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. **액세스 제어**에서 다음 옵션을 선택 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **앱 보호 정책 필요 (미리 보기)**
   - **선택된 컨트롤 중 하나가 필요**   
 
      ![조건부 액세스](./media/app-protection-based-conditional-access/11.png)

**2 단계: ActiveSync를 사용 하 여 Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange Online**을 선택 합니다. 

   ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

1. **조건**에서 **클라이언트 앱 (미리 보기)** 을 구성 합니다. 

   **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택 합니다.

   ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

1. **액세스 제어**에서 다음 옵션을 선택 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **앱 보호 정책 필요 (미리 보기)**
   - **선택된 컨트롤 중 하나가 필요**

      ![조건부 액세스](./media/app-protection-based-conditional-access/11.png)

**3 단계: iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune를 사용 하 여 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조 하세요.

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 및 준수 장치 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 및 준수 장치 조건부 액세스 정책으로 구성 됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.
 
- Exchange에 연결 하기 위해 iOS 또는 Android에서 네이티브 메일 응용 프로그램을 사용 하 여 전자 메일을 구성 합니다.
- 액세스를 위해 장치를 등록 해야 함을 나타내는 전자 메일을 받습니다.
- Intune 회사 포털를 다운로드 하 고 포털에 로그인 합니다.
- 메일을 확인 하 고 Outlook 앱을 사용 하 라는 메시지가 표시 됩니다.
- Outlook 앱을 다운로드 합니다.
- Outlook 앱을 열고 등록에 사용 되는 자격 증명을 입력 합니다.
- Intune 앱 보호 정책을 받을 수 있습니다.
- Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수 있습니다.

Intune 앱 보호 정책은 회사 데이터에 대 한 액세스가 부여 되기 전에 활성화 됩니다. 정책에서 사용자에 게 응용 프로그램을 다시 시작 하거나 추가 PIN을 사용 하 라는 메시지가 표시 될 수 있습니다. 이는 응용 프로그램 및 플랫폼에 대해 정책이 구성 된 경우에 해당 합니다.

### <a name="configuration"></a>구성

**1 단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/01.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange Online**을 선택 합니다. 

   ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

1. **조건**에서 **장치 플랫폼** 및 **클라이언트 앱 (미리 보기)** 을 구성 합니다. 
   1. **장치 플랫폼**에서 **Android** 및 **iOS**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

   1. **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

1. **액세스 제어**에서 다음 옵션을 선택 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **앱 보호 정책 필요 (미리 보기)**
   - **선택된 컨트롤이 모두 필요**   
 
      ![조건부 액세스](./media/app-protection-based-conditional-access/13.png)

**2 단계: ActiveSync를 사용 하 여 Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange Online**을 선택 합니다. 

   ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

1. **조건**에서 **클라이언트 앱 (미리 보기)** 을 구성 합니다. 

   **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택 합니다.

   ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

1. **액세스 제어**에서 다음 옵션을 선택 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **앱 보호 정책 필요 (미리 보기)**
   - **선택된 컨트롤이 모두 필요**   
 
      ![조건부 액세스](./media/app-protection-based-conditional-access/13.png)

**3 단계: iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune를 사용 하 여 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조 하세요.

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대 한 앱 보호 기반 또는 앱 기반 정책

이 시나리오는 Exchange Online 및 SharePoint Online에 액세스 하기 위한 앱 보호 기반 또는 승인 된 앱 정책으로 구성 됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- 앱 보호 정책 요구 사항이 나 승인 된 앱 요구 사항을 지 원하는 앱 목록에 있는 클라이언트 응용 프로그램을 구성 합니다.  
- 앱 보호 정책 요구 사항을 충족 하 고 Intune 앱 보호 정책을 받을 수 있는 클라이언트 응용 프로그램을 사용 합니다.
- Intune 앱 보호 정책을 지 원하는 승인 된 앱 정책 요구 사항을 충족 하는 클라이언트 응용 프로그램을 사용 합니다.
- 전자 메일 또는 문서에 액세스 하는 응용 프로그램을 엽니다.
- Outlook 응용 프로그램을 열고 Azure AD 자격 증명으로 로그인 합니다.
- IOS 용 Microsoft Authenticator를 설치할지 아니면 Android 사용 Intune 회사 포털 (아직 설치 되지 않은 경우)를 설치할지 묻는 메시지가 표시 됩니다.
- 응용 프로그램을 설치 하 고, 계속 하려면 Outlook 앱으로 돌아갈 수 있습니다.
- 장치를 등록 하 라는 메시지가 표시 됩니다.
- Intune 앱 보호 정책을 받을 수 있습니다.
- Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수 있습니다.
- 앱 보호 정책 요구 사항은 아니지만 승인 된 앱 요구 사항에 나열 된 앱을 사용 하 여 사이트 및 문서에 액세스할 수 있습니다.

회사 데이터에 대 한 액세스 권한을 부여 하기 전에 모든 Intune 앱 보호 정책이 필요 합니다. 정책에서 사용자에 게 응용 프로그램을 다시 시작 하거나 추가 PIN을 사용 하 라는 메시지가 표시 될 수 있습니다. 이는 응용 프로그램 및 플랫폼에 대해 정책이 구성 된 경우에 해당 합니다.

**주의**

- 앱 보호 기반 및 앱 기반 조건부 액세스 정책을 모두 지원 하려는 경우이 시나리오를 사용할 수 있습니다.
- 이 *또는* 정책에서 앱 보호 정책 요구 사항이 있는 앱은 승인 된 클라이언트 앱 요구 사항 이전에 액세스에 대해 평가 됩니다.

### <a name="configuration"></a>구성

**1 단계: Exchange Online 및 SharePoint Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서 조건부 액세스 정책의 경우 다음 구성 요소를 구성 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/62.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.
1. **할당**의 **사용자 및 그룹**에서 각 조건부 액세스 정책에 대해 하나 이상의 사용자 또는 그룹을 선택 합니다.
1. **클라우드 앱**에서 **Office 365 Exchange online** 및 **office 365 SharePoint online**을 선택 합니다. 

   ![조건부 액세스](./media/app-protection-based-conditional-access/02.png)

1. **조건**에서 **장치 플랫폼** 및 **클라이언트 앱 (미리 보기)** 을 구성 합니다. 
   1. **장치 플랫폼**에서 **Android** 및 **iOS**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

   1. **클라이언트 앱 (미리 보기)** 에서 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택 합니다.

      ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

1. **액세스 제어**에서 다음 옵션을 선택 합니다.
   - **승인 된 클라이언트 앱 필요**
   - **앱 보호 정책 필요 (미리 보기)**
   - **선택된 컨트롤 중 하나가 필요**
 
      ![조건부 액세스](./media/app-protection-based-conditional-access/12.png)

**2 단계: iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune를 사용 하 여 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대 한 조건부 액세스 정책을 구성할 준비가 되 면 [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md)를 참조 하세요.
