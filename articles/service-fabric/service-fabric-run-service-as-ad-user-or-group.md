---
title: AD 사용자 또는 그룹으로 Azure Service Fabric 서비스 실행 | Microsoft Docs
description: Service Fabric Windows 독립 실행형 클러스터에서 Active Directory 사용자 또는 그룹으로 서비스를 실행하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837728"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Active Directory 사용자 또는 그룹으로 서비스 실행
Windows Server 독립 실행형 클러스터의 경우, RunAs 정책을 사용하여 Active Directory 사용자 또는 그룹으로 서비스를 실행할 수 있습니다.  기본적으로 서비스 패브릭 애플리케이션은 Fabric.exe 프로세스가 실행하는 계정을 통해 실행됩니다. 서로 다른 계정에서 애플리케이션을 실행하면 공유 호스트 환경에서도 서로를 더욱 안전하게 보호할 수 있습니다. 여기에는 Azure AD(Azure Active Directory)가 아닌 도메인 내의 Active Directory 온-프레미스가 사용됩니다.  [gMSA(그룹 관리 서비스 계정)](service-fabric-run-service-as-gmsa.md)로 서비스를 실행할 수도 있습니다.

도메인 사용자 또는 그룹을 사용하여 권한이 부여된 도메인의 다른 리소스(예: 파일 공유)에 액세스할 수 있습니다.

다음 예제는 *TestUser*라는 Active Directory 사용자와 *MyCert*라는 인증서를 사용하여 암호화된 도메인 암호를 보여 줍니다. `Invoke-ServiceFabricEncryptText` PowerShell 명령을 사용하여 암호 텍스트를 만들 수 있습니다. 자세한 내용은 [Service Fabric 애플리케이션의 비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

암호 해독을 위한 인증서의 개인 키는 대역 외 메서드를 사용하여(Azure에서는 Azure Resource Manager를 통해) 로컬 컴퓨터에 배포해야 합니다. 그런 다음 Service Fabric이 컴퓨터에 서비스 패키지를 배포할 때 사용자 이름과 함께 암호를 해독하고 이 자격 증명으로 실행하도록 Active Directory로 인증할 수 있습니다.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> 서비스에 RunAs 정책을 적용하고 서비스 매니페스트가 HTTP 프로토콜로 엔드포인트 리소스를 선언하면 **SecurityAccessPolicy**도 지정해야 합니다.  자세한 내용은 [HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당](service-fabric-assign-policy-to-endpoint.md)을 참조하세요. 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
다음 단계는 아래 문서를 참조하세요.
* [애플리케이션 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
