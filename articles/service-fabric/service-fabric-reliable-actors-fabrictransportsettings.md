---
title: Azure 서비스 패브릭 행위자에서 FabricTransport 설정 변경 | Microsoft Docs
description: Azure Service Fabric 행위자 통신 설정에 대해 알아봅니다.
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: b6cff24e5a7812a88673d80476819e51e6f5da35
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125071"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Reliable Actors에 대한 FabricTransport 설정 구성

구성할 수 있는 설정은 다음과 같습니다.
- C#: [FabricTransportRemotingSettings](
https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [FabricTransportRemotingSettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

다음과 같은 방식으로 기본 FabricTransport 구성을 수정할 수 있습니다.

## <a name="assembly-attribute"></a>어셈블리 특성

[FabricTransportActorRemotingProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN) 특성은 행위자 클라이언트 및 행위자 서비스 어셈블리에 적용해야 합니다.

다음 예제에서는 FabricTransport OperationTimeout 설정의 기본값을 변경하는 방법을 보여 줍니다.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   두 번째 예에서는 FabricTransport MaxMessageSize 및 OperationTimeoutInSeconds의 기본 값을 변경합니다.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>구성 패키지

[구성 패키지](service-fabric-application-and-service-manifests.md)를 사용하여 기본 구성을 수정할 수 있습니다.

> [!IMPORTANT]
> Linux 노드에서 인증서는 PEM 형식이어야 합니다. Linux에서 인증서 찾기 및 구성에 대해 자세히 알아보려면 [Linux에서 인증서 구성](./service-fabric-configure-certificates-linux.md)을 참조하세요. 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>행위자 서비스에 대한 FabricTransport 설정 구성

settings.xml 파일에 TransportSettings 섹션을 추가합니다.

기본적으로 행위자 코드는 "&lt;ActorName&gt;TransportSettings"로 SectionName을 찾습니다. 찾을 수 없는 경우 "TransportSettings"로 sectionName을 확인합니다.

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>행위자 클라이언트 어셈블리에 대한 FabricTransport 설정 구성

클라이언트를 서비스의 일부로 실행하지 않는 경우에는 client exe 파일과 같은 위치에 "&lt;Client Exe Name&gt;.settings.xml" 파일을 만들 수 있습니다. 그런 다음 해당 파일에서 TransportSettings 섹션을 추가합니다. SectionName은 "TransportSettings"여야 합니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* 보조 인증서로 보안 행위자 서비스/클라이언트에 대한 FabricTransport 설정 구성.
  CertificateFindValuebySecondary 매개 변수를 추가하여 보조 인증서 정보를 추가할 수 있습니다.
  다음은 수신기 TransportSettings에 대한 예제입니다.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   다음은 클라이언트 TransportSettings에 대한 예제입니다.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * 주체 이름을 사용한 행위자 서비스/클라이언트 보안을 위한 FabricTransport 설정 구성.
    사용자는 FindBySubjectName,add CertificateIssuerThumbprints 및 CertificateRemoteCommonNames 값으로 findType을 제공해야 합니다.
    다음은 수신기 TransportSettings에 대한 예제입니다.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    다음은 클라이언트 TransportSettings에 대한 예제입니다.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
