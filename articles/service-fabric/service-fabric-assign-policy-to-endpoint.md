---
title: Azure Service Fabric 서비스 엔드포인트에 액세스 정책 할당 | Microsoft Docs
description: Service Fabric 서비스의 HTTP 또는 HTTPS 엔드포인트에 보안 액세스 정책을 할당하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614196"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당
실행(run-as) 정책을 적용하고 서비스 매니페스트가 HTTP 엔드포인트 리소스를 선언하면 **SecurityAccessPolicy**를 지정해야 합니다.  **SecurityAccessPolicy**는 이러한 엔드포인트에 할당된 포트가 서비스를 실행하는 사용자 계정으로 제대로 제한되도록 합니다. 그러지 않으면 **http.sys** 가 해당 서비스에 액세스할 수 없고 클라이언트의 호출과 함께 오류가 발생합니다. 다음은 **EndpointName**이라는 엔드포인트에 Customer1 계정을 적용하여 전체 액세스 권한을 부여하는 예제입니다.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

HTTPS 엔드포인트의 경우, 클라이언트에 반환할 인증서의 이름도 표시합니다. 인증서는 **EndpointBindingPolicy**를 사용하여 참조합니다.  인증서는 애플리케이션 매니페스트의 **인증서** 섹션에 정의되어 있습니다.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> HTTPS를 사용하는 경우 동일한 노드에 배포된 다른 서비스 인스턴스(애플리케이션과 독립적)에 동일한 포트 및 인증서를 사용하지 않습니다. 다른 애플리케이션 인스턴스에서 동일한 포트를 사용하는 두 가지 다른 서비스를 업그레이드하면 업그레이드 오류가 발생합니다. 자세한 내용은 [HTTPS 엔드포인트로 여러 애플리케이션 업그레이드](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)를 참조하세요.
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
다음 단계는 아래 문서를 참조하세요.
* [애플리케이션 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
