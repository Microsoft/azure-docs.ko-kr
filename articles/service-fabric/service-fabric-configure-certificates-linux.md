---
title: Linux에서 Azure Service Fabric 애플리케이션에 대한 인증서 구성 | Microsoft Docs
description: Linux 클러스터에서 Service Fabric 런타임을 사용하여 앱에 대한 인증서 구성
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: c0580b75544a9613bc8caf2faaac11ba1ba6708e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881371"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Linux 클러스터의 인증서 및 보안

이 문서에서는 Linux 클러스터에서 X.509 인증서 구성에 대한 정보를 제공합니다.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Linux 노드에서 X.509 인증서의 위치 및 형식

Service Fabric은 일반적으로 X.509 인증서가 Linux 클러스터 노드의 */var/lib/sfcerts* 디렉터리에 존재할 것으로 예상합니다. 클러스터 인증서, 클라이언트 인증서 등의 경우에 그렇습니다. 경우에 따라 인증서에 대해 *var/lib/sfcerts* 폴더 이외의 위치를 지정할 수 있습니다. 예를 들어 Service Fabric Java SDK를 사용하여 빌드된 Reliable Services를 사용하면 일부 애플리케이션 특정 인증서에 대한 구성 패키지(Settings.xml)를 통해 다른 위치를 지정할 수 있습니다. 자세히 알아보려면 [구성 패키지(Settings.xml)에서 참조되는 인증서](#certificates-referenced-in-the-configuration-package-settingsxml)를 참조하세요.

Linux 클러스터의 경우 Service Fabric은 인증서가 인증서와 개인 키를 포함하는 .pem 파일 또는 인증서를 포함하는 .crt 파일 및 개인 키를 포함하는 .key 파일로 존재할 것으로 예상합니다. 모든 파일은 PEM 형식이어야 합니다. 

[Resource Manager 템플릿](./service-fabric-cluster-creation-create-template.md) 또는 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) 명령을 사용하여 Azure Key Vault에서 인증서를 설치하는 경우 인증서는 각 노드의 */var/ lib/sfcerts* 디렉터리에 올바른 형식으로 설치됩니다. 다른 방법을 통해 인증서를 설치하는 경우 클러스터 노드에 인증서가 올바르게 설치되어 있는지 확인해야 합니다.

## <a name="certificates-referenced-in-the-application-manifest"></a>애플리케이션 매니페스트에서 참조되는 인증서

애플리케이션 매니페스트에서 지정된 인증서(예: [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) 또는 [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) 요소를 통해)는 */var/lib/sfcerts* 디렉터리에 존재해야 합니다. 애플리케이션 매니페스트에서 인증서를 지정하는 데 사용되는 요소는 경로 특성을 사용하지 않으므로 인증서는 기본 디렉터리에 존재해야 합니다. 이러한 요소는 선택적 **X509StoreName** 특성을 사용합니다. 기본값은 Linux 노드에서 */var/lib/sfcerts* 디렉터리를 가리키는 "My"입니다. Linux 클러스터에서 다른 값은 정의되지 않았습니다. Linux 클러스터에서 실행되는 앱에 대해 **X509StoreName** 특성을 생략하는 것이 좋습니다. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>구성 패키지(Settings.xml)에서 참조되는 인증서

일부 서비스의 경우 [ConfigPackage](./service-fabric-application-and-service-manifests.md)에서 X.509 인증서를 구성할 수 있습니다(기본적으로 Settings.xml). 예를 들어 Service Fabric .NET Core 또는 Java SDK를 사용하여 빌드된 Reliable Services 서비스에 대한 RPC 채널의 보안을 유지하는 데 사용되는 인증서를 선언하는 경우입니다. 구성 패키지에서 인증서를 참조하는 두 가지 방법이 있습니다. 지원은 .NET Core 및 Java SDK에 따라 다릅니다.

### <a name="using-x509-securitycredentialstype"></a>X509 SecurityCredentialsType 사용

.NET 또는 Java SDK를 사용하여 **SecurityCredentialsType**에 대해 **X509**를 지정할 수 있습니다. 이는 `SecurityCredentials`([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials))의 `X509Credentials`([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) 형식에 해당합니다.

**X509** 참조는 인증서 저장소에서 인증서를 찾습니다. 다음 XML은 인증서의 위치를 지정하는 데 사용되는 매개 변수를 보여줍니다.

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Linux에서 실행되는 서비스의 경우 **LocalMachine**/**My**는 인증서에 대한 기본 위치, */var/lib/sfcerts* 디렉터리를 가리킵니다. Linux의 경우 **CertificateStoreLocation** 및 **CertificateStoreName**의 다른 조합은 정의되지 않습니다. 

항상 **CertificateStoreLocation** 매개 변수에 대해 **LocalMachine**을 지정합니다. 기본값은 "My"이므로 **CertificateStoreName** 매개 변수를 지정할 필요가 없습니다. **X509** 참조를 사용하여 인증서 파일은 클러스터 노드의 */var/lib/sfcerts* 디렉터리에 있어야 합니다.  

다음 XML은 이 스타일에 따른 **TransportSettings** 섹션을 보여줍니다.

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>X509_2 SecurityCredentialsType 사용

Java SDK를 사용하여 **SecurityCredentialsType**에 대해 **X509_2**를 지정할 수 있습니다. 이는 `SecurityCredentials`([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials))의 `X509Credentials2`([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) 형식에 해당합니다. 

**X509_2** 참조를 사용하여 경로 매개 변수를 지정하므로 */var/lib/sfcerts* 이외의 디렉터리에서 인증서를 찾을 수 있습니다.  다음 XML은 인증서의 위치를 지정하는 데 사용되는 매개 변수를 보여줍니다. 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

다음 XML은 이 스타일에 따른 **TransportSettings** 섹션을 보여줍니다.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> 인증서는 이전 XML에서 .crt 파일로 지정됩니다. 이는 동일한 위치에 개인 키를 포함하는 .key 파일도 있음을 의미합니다.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Linux 클러스터에서 실행하도록 Reliable Services 앱 구성

Service Fabric SDK를 사용하면 Service Fabric 런타임 API와 통신하여 플랫폼을 활용할 수 있습니다. 보안 Linux 클러스터에서 이 기능을 사용하는 모든 애플리케이션을 실행할 때 Service Fabric 런타임으로 유효성을 검사하는 데 사용할 수 있는 인증서로 애플리케이션을 구성해야 합니다. .NET Core 또는 Java SDK를 사용하여 작성된 Service Fabric Reliable Service 서비스를 포함하는 애플리케이션에는 이 구성이 필요합니다. 

애플리케이션을 구성하려면 *ApplicationManifest.xml* 파일의 **ApplicationManifest** 태그 아래에 있는 **Certificates** 태그 아래의 [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) 요소를 추가합니다. 다음 XML은 해당 지문으로 참조되는 인증서를 보여줍니다. 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

각 클러스터 노드에 설치하는 클러스터 인증서 또는 인증서를 참조할 수 있습니다. Linux에서 인증서 파일은 */var/lib/sfcerts* 디렉터리에 존재해야 합니다. 자세히 알아보려면 [Linux 노드에서 X.509 인증서의 위치 및 형식](#location-and-format-of-x509-certificates-on-linux-nodes)을 참조하세요.



