---
title: Media Services 계정에 대 한 역할 기반 액세스 제어-Azure | Microsoft Docs
description: 이 문서에서는 Azure Media Services 계정의 RBAC (역할 기반 액세스 제어)에 대해 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: a29280815a1c9cf3d578f0d304bc679bf234c185
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267125"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Media Services 계정에 대 한 RBAC (역할 기반 액세스 제어)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

현재 Azure Media Services는 서비스와 관련 된 사용자 지정 역할을 정의 하지 않습니다. Media Services 계정에 대 한 모든 권한을 얻으려면 고객은 **소유자** 또는 **참가자**의 기본 제공 역할을 사용할 수 있습니다. 이러한 역할의 주요 차이점은 **소유자** 는 리소스에 대 한 액세스 권한이 있는 사용자를 제어할 수 있으며 **참가자** 는 그렇지 않습니다. 기본 제공 **판독기** 역할을 사용할 수도 있지만 사용자 또는 응용 프로그램은 Media Services api에 대 한 읽기 권한만 갖습니다. 

## <a name="design-principles"></a>디자인 원칙

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 Api는 **가져오기** 또는 **나열** 작업에 대 한 암호 또는 자격 증명을 반환 하지 않습니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 사용자는 별도의 작업 메서드를 호출 하 여 암호 또는 자격 증명을 가져와야 합니다. **판독기** 역할은 ListContainerSas, StreamingLocator. ListContentKeys, contentkeypolicy 등의 작업을 호출할 수 없습니다. 별도의 작업을 사용 하면 원하는 경우 사용자 지정 역할에서 보다 세분화 된 RBAC 보안 권한을 설정할 수 있습니다.

Media Services에서 지 원하는 작업을 나열 하려면 다음을 수행 합니다.

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[기본 제공 역할 정의](../../role-based-access-control/built-in-roles.md) 문서에서는 역할이 부여 하는 내용을 정확히 알려 줍니다. 

자세한 내용은 다음 문서를 참조하세요.

- [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
- [RBAC를 사용 하 여 액세스 관리](../../role-based-access-control/role-assignments-rest.md)
- [Media Services 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>다음 단계

- [Media Services v3 Api를 사용 하 여 개발](media-services-apis-overview.md)
- [Media Services .NET을 사용 하 여 콘텐츠 키 정책 가져오기](get-content-key-policy-dotnet-howto.md)
