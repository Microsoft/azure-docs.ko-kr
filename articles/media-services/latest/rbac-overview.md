---
title: Azure Media Services 계정에 대 한 역할 기반 액세스 제어 | Microsoft Docs
description: 이 문서에서는 Azure Media Services 계정에 대 한 역할 기반 액세스 제어 (RBAC)를 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 82c438ea246ba18fa7dac2281d68a3ea7ba41db8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930203"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Media Services 계정에 대 한 역할 기반 액세스 제어 (RBAC)

현재 Azure Media Services 서비스에 특정 사용자 정의 역할을 정의 하지 않습니다. Media Services 계정에 대 한 전체 액세스를 가져오려면 고객의 기본 제공 역할을 사용할 수 있습니다 **소유자** 하거나 **참가자**합니다. 이러한 역할 간의 주요 차이점은: 합니다 **소유자** 리소스에 액세스할 수 있는 사람을 제어할 수 있습니다 및 **참가자** 수 없습니다. 기본 제공 **판독기** 역할을 사용할 수도 있습니다 하지만 사용자 또는 응용 프로그램에서는 게 Media Services Api에 대 한 읽기 액세스입니다. 

## <a name="design-principles"></a>디자인 원칙

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 Api 반환 하지 않는 암호 또는 자격 증명에 **가져옵니다** 하거나 **목록** 작업 합니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 사용자 암호 또는 자격 증명 얻기 위해 별도 작업 메서드를 호출 해야 합니다. 합니다 **판독기** 역할 Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets 등의 작업을 호출할 수 없습니다. 별도 작업이 필요 합니다. 원하는 경우 사용자 지정 역할을 더 세부적인 RBAC 보안 권한을 설정할 수 있습니다.

Media Services 지원 작업을 나열 하려면 다음을 수행 합니다.

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

합니다 [기본 제공 역할 정의](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 문서를 알려 정확히 어떤 역할을 부여 합니다. 

자세한 내용은 다음 문서를 참조 하세요.

- [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 관리자 역할](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Azure 리소스에 대 한 RBAC 란?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [RBAC를 사용 하 여 액세스를 관리 하려면](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>다음 단계

- [V3 Api를 Media Services를 사용 하 여 개발](media-services-apis-overview.md)
- [Media Services.NET을 사용 하 여 콘텐츠 키 정책 가져오기](get-content-key-policy-dotnet-howto.md)
