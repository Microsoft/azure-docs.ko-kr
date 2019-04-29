---
title: Azure Service Fabric에서 실행되는 컨테이너에 인증서 가져오기 | Microsoft Docs
description: 이제 Service Fabric 컨테이너 서비스에 인증서 파일을 가져오는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: ac461c0d512a5a58ce687623a53e99eeeca399e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837694"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Service Fabric에서 실행되는 컨테이너에 인증서 파일 가져오기

인증서를 지정하여 컨테이너 서비스를 보호할 수 있습니다. Service Fabric은 컨테이너 내부에 Windows 또는 Linux 클러스터(5.7 이상 버전)의 노드에 설치된 인증서에 액세스하는 서비스의 메커니즘을 제공합니다. 인증서는 클러스터의 모든 노드에서 LocalMachine 아래 인증서 저장소에 설치해야 합니다. 인증서에 해당하는 개인 키는 사용이 가능하고, 액세스가 가능하며 Windows에서 내보낼 수 있어야 합니다. 인증서 정보는 다음 코드 조각이 표시한 대로 `ContainerHostPolicies` 태그의 애플리케이션 매니페스트에서 제공됩니다.

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows 클러스터의 경우 애플리케이션을 시작할 때 런타임은 참조된 각 인증서와 해당 개인 키를 임의로 생성된 암호로 보호되는 PFX 파일로 내보냅니다. PFX 및 암호 파일은 각각 다음 환경 변수를 사용하여 컨테이너 내부에 액세스할 수 있습니다. 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux 클러스터의 경우 인증서(PEM)가 X509StoreName으로 지정된 저장소에서 컨테이너로 복사됩니다. Linux의 해당 환경 변수는 다음과 같습니다.

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

또는 필요한 형식의 인증서가 있고 컨테이너 내에서 인증서에 액세스하려는 경우 앱 패키지에 데이터 패키지를 만들고 애플리케이션 매니페스트 내에서 다음을 지정합니다.

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

컨테이너 서비스 또는 프로세스를 사용하여 인증서 파일을 컨테이너로 가져옵니다. 인증서를 가져오기 위해 컨테이너 프로세스 내에서 `setupentrypoint.sh` 스크립트를 사용하거나 사용자 지정 코드를 실행할 수 있습니다. PFX 파일을 가져오는 C#로 작성된 샘플 코드는 다음과 같습니다.

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
이 PFX 인증서는 애플리케이션이나 서비스를 인증하거나 다른 서비스와 통신을 보호하는 데 사용될 수 있습니다. 기본적으로 파일은 시스템에만 ACL됩니다. 서비스에 필요한 다른 계정에 ACL할 수 있습니다.

다음 단계는 아래 문서를 참조하세요.

* [Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers.md)
* [Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers-linux.md)
