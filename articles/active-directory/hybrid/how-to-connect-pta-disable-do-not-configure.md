---
title: Azure AD Connect "구성 안 함"을 사용하는 경우 PTA를 사용하지 않도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect "구성 안 함" 기능을 사용하여 PTA를 사용하지 않도록 설정하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919938"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Azure AD Connect를 사용할 때 PTA 사용하지 않도록 설정

Azure AD Connect에서 PTA(통과 인증)이 **"구성 안 함"** 으로 설정되어 있는 경우 이를 사용하지 않도록 설정할 수 있습니다. 

>[!NOTE]
>이미 PHS를 사용하도록 설정해 놓은 경우 PTA를 사용하지 않도록 설정하면 테넌트 대체는 PHS로 바뀝니다.

다음 cmdlet을 사용하여 PTA를 사용하지 않도록 설정할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
필요한 필수 구성 요소는 다음과 같습니다.
- PTA 에이전트가 설치된 모든 Windows 컴퓨터 
- 에이전트는 1.5.1742.0 버전 이상이어야 합니다. 
- Azure 전역 관리자 계정을 통해 PowerShell cmdlet을 실행하여 PTA를 사용하지 않도록 설정합니다.

>[!NOTE]
> 에이전트가 오래된 경우 이 작업을 완료하는 데 필요한 cmdlet이 없을 수 있습니다. Azure Portal에서 새 에이전트를 가져와 Windows 컴퓨터에 설치하고 관리자 자격 증명을 제공할 수 있습니다. 에이전트를 설치해도 클라우드의 PTA 상태는 영향을 받지 않습니다.

> [!IMPORTANT]
> Azure Government 클라우드를 사용하는 경우 다음 값을 사용하여 ENVIRONMENTNAME 매개 변수를 전달해야 합니다. 
>
>| 환경 이름 | 클라우드 |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>PTA를 사용하지 않도록 설정하려면
PowerShell 세션 내에서 다음을 사용하여 PTA를 사용하지 않도록 설정합니다.
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 또는 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 또는 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>에이전트에 액세스할 수 없는 경우

에이전트 컴퓨터가 없는 경우 다음 명령을 사용하여 에이전트를 설치할 수 있습니다.

1. portal.azure.com에서 최신 인증 에이전트를 다운로드합니다.
2. 기능 설치: `.\AADConnectAuthAgentSetup.exe` 또는 `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 통과 인증으로 사용자 로그인](how-to-connect-pta.md)
