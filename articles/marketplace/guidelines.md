---
title: Azure Marketplace 및 AppSource 게시자에 대한 지침 | Azure
description: 앱 및 서비스 게시자에 대한 Azure Marketplace 및 AppSource 지침
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: a0ab88b1375f5178ca6f41689de8d2f30f8d7808
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739444"
---
# <a name="guidelines"></a>지침  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Azure Marketplace에 대한 지침  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Marketplace 계정 관리를 위한 Microsoft ID 만들기 지침  
두 명 이상의 사용자가 Marketplace 계정을 만드는 데 사용된 것과 동일한 Microsoft ID에 액세스해야 하는 경우 다음 지침을 참조하여 회사 계정을 만들어야 합니다. 

>[!IMPORTANT]
>여러 사용자가 Microsoft 개발자 센터(개발자 센터) 계정에 액세스하도록 권한을 부여하려면 Azure AD(Azure Active Directory)를 사용하여 개별 사용자에게 역할을 할당하는 것이 좋습니다. 각 사용자는 개별 Azure AD 자격 증명으로 로그인하여 계정에 액세스해야 합니다. 회사에 등록된 도메인의 메일 주소를 사용하여 Microsoft ID를 만듭니다. 해당 메일은 개인에게 할당하지 않는 것이 좋습니다. 예는 `windowsapps@fabrikam.com`입니다.  
>*   자세한 내용은 참조는 [문제: Azure AD에서 Microsoft ID 페더레이션 도메인](#issue-microsoft-id-in-an-azure-ad-federated-domain) 섹션입니다.  

*   Microsoft ID에 대한 액세스는 최소한의 개발자 수로 제한합니다. 
*   개발자 센터 계정에 액세스해야 하는 모든 사용자가 포함된 회사 메일 DL(배포 목록)을 설정합니다. 보안 정보에 DL 메일 주소를 추가합니다. DL을 사용하면 목록에 있는 모든 직원이 요청할 때 보안 코드를 받고 Microsoft ID의 보안 정보를 관리할 수 있습니다. 배포 목록 설정이 적절하지 않은 경우, 메시지가 표시되면 개별 메일 계정의 소유자가 보안 코드를 액세스 및 공유할 수 있어야 합니다.  
    *   예를 들어 새 보안 정보가 Microsoft ID에 추가되거나 새 디바이스에서 Microsoft ID에 액세스하는 경우 소유자에게 메시지가 표시됩니다.  
*   내선번호가 필요가 없고 핵심 팀 구성원에 연락 가능한 회사 전화 번호를 추가합니다.  
*   일반적으로 개발자는 신뢰할 수 있는 디바이스를 사용하여 개발자 센터 계정에 로그인해야 합니다. 모든 핵심 팀 구성원은 이러한 신뢰할 수 있는 디바이스에 액세스할 수 있어야 합니다. 신뢰할 수 있는 디바이스를 사용하면 개발자 센터 계정에 액세스하려는 사용자에게 보안 코드를 전송하는 작업을 줄일 수 있습니다.  
*   신뢰할 수 없는 컴퓨터에서 개발자 센터 계정에 액세스 권한을 부여해야 하는 경우 액세스 권한을 5명 이하로 제한해야 합니다. 이상적으로 개발자는 지리적 위치나 네트워크 위치가 동일한 컴퓨터에서 계정에 액세스해야 합니다.  
*   보안 정보를 자주 검토하고 확인합니다.  
    *   보안 정보를 보려면 보안 설정 페이지([account.live.com/proofs/Manage](https://account.live.com/proofs/Manage))를 방문하세요.

개발자 센터 계정은 주로 신뢰할 수 있는 컴퓨터에서 액세스해야 합니다. 한 주마다 개발자 센터 계정당 생성되는 코드 수에는 제한이 있기 때문에 신뢰할 수 있는 컴퓨터에서 액세스하는 것이 중요합니다. 신뢰할 수 있는 컴퓨터를 사용하면 가장 안전하고 일관적인 로그인 환경을 구현할 수 있습니다. 
*   추가 개발자 센터 계정 지침 및 보안에 대한 자세한 내용은 개발자 계정 열기 페이지([docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account))를 참조하세요. 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>문제: Azure AD 페더레이션된 도메인의 Microsoft ID  
회사 계정은 Azure AD(Azure Active Directory)를 통해 페더레이션될 수 있습니다. Azure AD와 페더레이션된 회사 메일 주소를 사용하여 Microsoft ID를 만들려고 하면 오류가 발생합니다. 오류가 발생하면 IT 팀에 문의하여 Azure AD를 통해 자신의 계정이 페더레이션되었는지 확인해야 합니다. Azure AD 페더레이션 메일은 알려진 문제이며 Microsoft는 이를 해결하기 위해 노력하고 있습니다.  
*   Azure AD에 대한 자세한 내용은 Azure Active Directory 설명서 페이지([docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory))를 참조하세요.

Microsoft는 한 가지 해결 방법을 권장합니다. 다음 단계를 수행하여 `outlook.com` 도메인에 새 메일 주소를 만들고 통신을 전달할 규칙을 만듭니다.  
1.  계정 만들기 페이지로 이동하여 새 메일 주소 받기 링크를 클릭합니다. 
    *   Microsoft ID에 등록하려면 계정 만들기 페이지([signup.live.com/signup](https://signup.live.com/signup))를 방문하세요.  
2.  새 전자 메일 주소를 만들고 암호를 입력합니다. `outlook.com` 도메인에 새로운 Microsoft ID와 메일 사서함이 만들어집니다. 계정이 생성될 때까지 등록 프로세스를 계속 진행합니다.  

    >[!IMPORTANT]
    >개발자 센터에 등록하려면 Microsoft ID로 등록된 메일 주소 또는 배포 목록을 사용해야 합니다. Microsoft는 배포 목록을 사용하여 개인에 대한 종속성을 제거하는 것을 권장합니다. 메일 주소나 배포 목록이 등록되지 않은 경우 지금 등록해야 합니다.    

    >[!Important]
    >메일 주소가 `Microsoft` 회사 도메인에 있는 경우 이 메일 주소를 사용하여 개발자 센터에 등록할 수 없습니다.  

3.  Outlook 메일 주소로 Microsoft ID를 만든 다음, Outlook 사서함에 로그인합니다. 메일 전달 규칙을 만듭니다. 메일 전달 규칙은 Outlook 사서함에 수신된 모든 메일을 Marketplace 계정을 관리하기 위해 만든 도메인의 메일 주소로 이동해야 합니다.  
    *   Outlook 사서함에 로그인하려면 Outlook 페이지([outlook.live.com/owa](https://outlook.live.com/owa))를 방문하세요.  
    *   전달 규칙에 대한 자세한 내용은 Outlook Web App에서 규칙을 사용하여 메시지를 다른 계정으로 자동 전달 페이지([support.office.com/article/ Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed))를 참조하세요.  

1.  전달 규칙은 Outlook 메일 계정에 수신된 모든 메일 및 통신을 회사에 등록된 도메인의 메일 주소로 보냅니다. `outlook.com` 메일 주소는 개발자 센터 및 Cloud 파트너 포털에서 모두 인증에 사용되어야 합니다.  

## <a name="next-steps"></a>다음 단계
*   [Azure Marketplace 및 AppSource 게시자 가이드](./marketplace-publishers-guide.md) 페이지를 방문합니다.  
 
---
