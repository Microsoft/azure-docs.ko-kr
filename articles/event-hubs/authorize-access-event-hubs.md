---
title: Azure Event Hubs에 대한 액세스 권한 부여
description: 이 문서에서는 Azure Event Hubs 리소스에 대한 액세스 권한을 부여 하는 여러 옵션에 대한 정보를 제공 합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: b5a037eaf310aa28c76d831dc9fe56eefaddbe56
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123473"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Azure Event Hubs에 대한 액세스 권한 부여
이벤트 허브에서 이벤트/데이터를 게시 하거나 사용할 때마다 클라이언트가 Event Hubs 리소스에 액세스 하려고 합니다. 보안 리소스에 대한 모든 요청은 서비스에서 클라이언트에 데이터를 게시/사용 하는 데 필요한 권한이 있는지 확인할 수 있도록 권한을 부여 해야 합니다. 

Azure Event Hubs는 보안 리소스에 대한 액세스 권한을 부여 하는 다음과 같은 옵션을 제공 합니다.

## <a name="azure-active-directory"></a>Azure Active Directory
Event Hubs 리소스에 대한 Azure Active Directory (Azure AD) 통합은 클라이언트의 리소스 액세스를 세부적으로 제어 하기 위해 RBAC (역할 기반 액세스 제어)를 제공 합니다. RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 OAuth 2.0 토큰을 반환 하기 위해 Azure AD에서 인증 됩니다. 토큰은 Event Hubs 리소스에 대한 액세스 요청에 권한을 부여 하는 데 사용할 수 있습니다.

Azure AD를 사용 하 여 인증 하는 방법에 대한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Active Directory를 사용 하 여 Azure Event Hubs에 대한 요청 인증](authenticate-application.md)
- [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대한 액세스 권한을 부여](authorize-access-azure-active-directory.md)합니다.

## <a name="shared-access-signatures"></a>공유 액세스 서명 
Event Hubs 리소스에 대한 SAS (공유 액세스 서명)는 Event Hubs 리소스에 대해 제한 된 위임 된 액세스를 제공 합니다. 서명이 유효한 시간 간격에 대한 제약 조건을 추가 하거나 부여 하는 사용 권한에 따라 리소스 관리의 유연성이 제공 됩니다. 자세한 내용은 [SAS (공유 액세스 서명)를 사용 하 여 인증](authenticate-shared-access-signature.md)을 참조 하세요. 

Azure AD에서 반환 된 OAuth 2.0 토큰을 사용 하는 사용자 또는 응용 프로그램에 대한 권한 부여는 SAS (공유 액세스 서명)를 통해 뛰어난 보안과 사용 편의성을 제공 합니다. Azure AD를 사용 하면 코드를 사용 하 여 액세스 토큰을 저장 하지 않아도 되며 잠재적 보안 취약성이 발생할 수 있습니다. SAS (공유 액세스 서명)를 계속 사용 하 여 Event Hubs 리소스에 대한 세분화 된 액세스 권한을 부여할 수 있지만, Azure AD는 SAS 토큰을 관리 하거나 손상 된 SAS를 해지 하는 것에 대해 걱정할 필요 없이 유사한 기능을 제공 합니다. 

기본적으로 모든 Event Hubs 리소스는 보안 되며 계정 소유자만 사용할 수 있습니다. 위에서 설명한 권한 부여 전략 중 하나를 사용 하 여 클라이언트에 이벤트 허브 리소스에 대한 액세스 권한을 부여할 수 있습니다. Azure Resource Manager 배포 모델로 만든 Event Hubs 리소스만 Azure AD 권한 부여를 지원 합니다. 최대한의 보안과 사용 편의성을 위해 가능한 경우 Azure AD를 사용 하는 것이 좋습니다.

SAS를 사용한 권한 부여에 대한 자세한 내용은 [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에](authorize-access-shared-access-signature.md)대한 액세스 권한 부여를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- GitHub 리포지토리에 게시 된 [RBAC 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 을 검토 합니다. 
- 다음 문서를 참조하세요.
    - [Azure Active Directory를 사용 하 여 응용 프로그램에서 Azure Event Hubs에 대한 요청 인증](authenticate-application.md)
    - [Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증](authenticate-managed-identity.md)
    - [공유 액세스 서명을 사용 하 여 Azure Event Hubs에 대한 요청 인증](authenticate-shared-access-signature.md)
    - [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
    - [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)

