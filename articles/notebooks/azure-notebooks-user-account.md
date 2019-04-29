---
title: Notebooks에 로그인
description: Microsoft 계정 또는 회사/학교 계정을 사용하여 Azure Notebooks에 대한 사용자 계정을 구성합니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 311f4d612c9deab23e7537b0c53ff3932c312cd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632142"
---
# <a name="your-user-account-for-azure-notebooks"></a>Azure Notebooks에 대한 사용자 계정

사용자 계정에 로그인하여 또는 로그인하지 않고 Azure Notebooks를 사용할 수 있습니다.

- 로그인하지 않을 경우 Notebook을 만들고 실행할 수 있지만 프로젝트의 일부로 Notebook 또는 데이터 파일을 유지할 수는 없습니다. 예를 들어 Azure Notebook에 대한 링크를 받은 사람은 로그인하지 않고 Notebook을 사용할 수 있습니다.
- 로그인하면 Azure Notebooks가 모든 프로젝트를 계정과 함께 유지합니다. 또한 로그인한 사용자는 사용자 ID를 통해 프로젝트 및 Notebook을 다른 사용자와 공유할 수 있습니다.
  - Azure Notebooks에 사용되는 계정을 Azure 구독과 연결하면 보다 강력한 서버에서 Notebook을 실행하고, 개인용 Notebook을 만들고, 개별 사용자에 대한 권한을 노트북에 부여하는 등의 추가 혜택을 누릴 수 있습니다.

Azure Notebooks에 로그인하려면 Microsoft 계정 또는 "회사 또는 학교" 계정이 필요합니다. Notebook 페이지의 오른쪽 위에서 **로그인** 명령을 선택하면 계정을 입력하라는 메시지가 표시됩니다.

![Azure Notebooks 로그인 명령](media/accounts/sign-in-command.png)

Azure Notebooks에서 수행하는 모든 작업은 로그인하는 데 사용하는 계정과 연결됩니다. 또한 각 계정은 [사용자 프로필](azure-notebooks-user-profile.md)에서 고유한 사용자 ID가 있어야 합니다. 결과적으로 별도의 프로젝트 및 별도의 ID 세트를 유지해야 하는 경우 여러 계정으로 Azure Notebooks에 로그인하면 됩니다. 예를 들어 데이터 과학 팀의 모든 멤버가 개별 계정 및 회사 외부 사람들에게 작업을 제공하기 위한 공유 그룹 계정을 가질 수 있습니다. 마찬가지로 강사는 외부 컨설팅 또는 오픈 소스 작업에 사용되는 계정과 다른 교육 역할 계정을 유지할 수 있습니다.

## <a name="microsoft-accounts"></a>Microsoft 계정

Microsoft 계정은 Windows, Azure, outlook.com, OneDrive, XBox Live 등의 Microsoft 제품 및 서비스에 로그인하는 데 사용됩니다. 이러한 서비스를 사용하는 경우 Azure Notebooks에 사용할 수 있는 Microsoft 계정을 이미 갖고 있을 확률이 높습니다.

잘 모르겠으면 계정 프롬프트에서 **만들기** 명령을 선택합니다. 원하는 공급 기업의 이메일 주소를 사용하여 새 Microsoft 계정을 만들 수 있습니다.

![새 Microsoft 계정을 만드는 명령](media/accounts/create-new-microsoft-account.png)

> [!Note]
> 연결 된 계정에 이미 있는 전자 메일 주소를 사용 하 여 새 계정을 만들려는 경우 표시 될 수 있습니다 메시지를 "있습니다 수 없습니다. 여기에서 등록 작업을 사용 하 여 회사 또는 학교 전자 메일 주소입니다. Gmail 이나 yahoo! 같은 개인 전자 메일을 사용 하거나 새 Outlook 전자 메일을 받습니다. " 이 경우 새 계정을 만들지 않고 회사 전자 메일 주소를 로그인을 시도 합니다.

자녀 계정의 경우 Azure Notebooks 액세스가 기본적으로 차단됩니다. 자녀 계정으로 로그인하면 아래 오류가 표시됩니다.

![자녀 계정으로 로그인을 시도하면 발생하는 오류: 오류가 발생했습니다. 부모님이 액세스를 차단했습니다.](media/accounts/child-account-error.png)

액세스가 가능하려면 부모님이 다음 단계를 수행해야 합니다.

1. `https://account.live.com/mk`를 방문하여 부모 계정으로 로그인합니다.
1. 자녀와 관련하여 문제가 있는 섹션에서 **타사 앱에 대한 이 자녀의 액세스 권한 관리**를 선택합니다.
1. 다음 페이지에서 **액세스 사용**을 선택합니다.
1. 다음에 자녀 계정을 사용하여 Azure Notebooks에 로그인할 때 나타나는 권한 프롬프트에서 **예**를 선택합니다.

> [!Warning]
> Azure Notebooks용 타사 앱에 대한 액세스를 사용하면 모든 타사 앱에 액세스가 사용됩니다. 부모님께서는 신중하게 액세스를 사용하고 자녀의 활동을 면밀하게 모니터링하시기 바랍니다.

## <a name="work-or-school-accounts"></a>회사 또는 학교 계정

회사 또는 학교 계정은 조직의 관리자가 Office 365 같은 Microsoft 클라우드 서비스에 멤버 액세스를 사용하기 위해 생성되며, 도메인 조인 컴퓨터의 Windows에 로그인하는 계정으로도 사용됩니다. 회사 또는 학교 계정은 일반적으로 any-user@contoso.com처럼 조직의 이메일 주소를 사용합니다.

Azure Notebooks는 계정의 이메일 주소, 사용자의 브라우저 정보 등의 정보를 수집하거나 사용하므로(하지만 공개하지는 않음) 회사 또는 학교 계정으로 Azure Notebooks에 로그인하려면 관리자의 동의가 필요합니다. (브라우저 데이터는 사용량에 따라 기능을 최적화하는 데 사용됩니다.)

사용자가 개별적으로 동의할 수 없도록 제한되는 경우 조직 계정 관리자가 사용자 대신 동의해야 합니다. 이 경우 사용자에게 "이 애플리케이션에 액세스할 수 없음" 메시지가 표시됩니다.

![회사 또는 학교 계정을 사용하면 "이 애플리케이션에 액세스할 수 없음" 메시지 표시](media/accounts/consent-permissions-denied.png)

관리자로서 정보 사용에 동의하려면 [관리자 동의 페이지](https://notebooks.azure.com/account/adminConsent)의 프로세스를 진행합니다.

## <a name="next-steps"></a>다음 단계  

> [!div class="nextstepaction"]
> [프로필 및 사용자 ID 편집](azure-notebooks-user-profile.md)
