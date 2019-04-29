---
title: GMSA 계정으로 Azure Service Fabric 서비스 실행 | Microsoft Docs
description: Service Fabric Windows 독립 실행형 클러스터에서 gMSA로 서비스를 실행하는 방법을 알아봅니다.
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
ms.openlocfilehash: 5c3781c2111fff7483a7fb65bd7b2e69c2011d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837745"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>그룹 관리 서비스 계정으로 서비스 실행
Windows Server 독립 실행형 클러스터에서 RunAs 정책을 사용하여 gMSA(그룹 관리 서비스 계정)로 서비스를 실행할 수 있습니다.  기본적으로 서비스 패브릭 애플리케이션은 Fabric.exe 프로세스가 실행하는 계정을 통해 실행됩니다. 서로 다른 계정에서 애플리케이션을 실행하면 공유 호스트 환경에서도 서로를 더욱 안전하게 보호할 수 있습니다. 여기에는 Azure AD(Azure Active Directory)가 아닌 도메인 내의 Active Directory 온-프레미스가 사용됩니다. gMSA를 사용하면 애플리케이션 매니페스트에 저장되는 암호나 암호화된 암호가 없습니다.  [Active Directory 사용자 또는 그룹](service-fabric-run-service-as-ad-user-or-group.md)으로 서비스를 실행할 수도 있습니다.

다음 예에서는 *svc-Test$* 라는 gMSA 계정을 작성하는 방법, 해당 관리 서비스 계정을 클러스터 노드에 배포하는 방법 및 사용자 주체를 구성하는 방법을 보여줍니다.

필수 조건:
- 도메인에 KDS 루트 키가 필요합니다.
- Windows Server 2012 이상 기능 수준에 도메인이 있어야 합니다.

1. Active Directory 도메인 관리자가 `New-ADServiceAccount` commandlet을 사용하여 그룹 관리 서비스 계정을 만들고 `PrincipalsAllowedToRetrieveManagedPassword`에 Serivce Fabric 클러스터 노드가 모두 포함되도록 합니다. `AccountName`, `DnsHostName` 및 `ServicePrincipalName`은 고유해야 합니다.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. 각 Service Fabric 클러스터 노드(예: `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`)에서 gMSA를 설치하고 테스트합니다.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. 사용자 보안 주체를 구성하고 사용자를 참조하도록 RunAsPolicy를 구성합니다.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> 서비스에 RunAs 정책을 적용하고 서비스 매니페스트가 HTTP 프로토콜로 엔드포인트 리소스를 선언하면 **SecurityAccessPolicy**를 지정해야 합니다.  자세한 내용은 [HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당](service-fabric-assign-policy-to-endpoint.md)을 참조하세요. 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
다음 단계는 아래 문서를 참조하세요.
* [애플리케이션 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
