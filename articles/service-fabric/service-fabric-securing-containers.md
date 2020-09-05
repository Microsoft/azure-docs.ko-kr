---
title: 컨테이너에 인증서 가져오기
description: 이제 Service Fabric 컨테이너 서비스에 인증서 파일을 가져오는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 426aa2ebbfb87fe2c80e0d1aff3eeecbe0e2472d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050746"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Service Fabric에서 실행되는 컨테이너에 인증서 파일 가져오기

> [!NOTE]
> Azure에서 실행 되는 Service Fabric 클러스터의 경우 [Service Fabric 응용 프로그램 관리 id](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) 를 사용 하 여 컨테이너 내에서 응용 프로그램 인증서를 프로 비전 하는 것이 좋습니다. 관리 Id는 서비스 수준에서 비밀 및 인증서의 격리를 제공 하 고, 응용 프로그램 인증서 프로 비전은 인프라의 워크플로가 아닌 응용 프로그램의 워크플로에 포함 될 수 있도록 합니다. CertificateRef 메커니즘은 이후 릴리스에서 더 이상 사용 되지 않습니다.

인증서를 지정하여 컨테이너 서비스를 보호할 수 있습니다. Service Fabric은 컨테이너 내부에 Windows 또는 Linux 클러스터(5.7 이상 버전)의 노드에 설치된 인증서에 액세스하는 서비스의 메커니즘을 제공합니다. 인증서는 클러스터의 모든 노드에서 LocalMachine 아래 인증서 저장소에 설치해야 합니다. 인증서에 해당하는 프라이빗 키는 사용이 가능하고, 액세스가 가능하며 Windows에서 내보낼 수 있어야 합니다. 인증서 정보는 다음 코드 조각이 표시한 대로 `ContainerHostPolicies` 태그의 애플리케이션 매니페스트에서 제공됩니다.

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows 클러스터의 경우 애플리케이션을 시작할 때 런타임은 참조된 각 인증서와 해당 프라이빗 키를 임의로 생성된 암호로 보호되는 PFX 파일로 내보냅니다. PFX 및 암호 파일은 각각 다음 환경 변수를 사용하여 컨테이너 내부에 액세스할 수 있습니다. 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux 클러스터의 경우 인증서(PEM)가 X509StoreName으로 지정된 저장소에서 컨테이너로 복사됩니다. Linux의 해당 환경 변수는 다음과 같습니다.

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

`PEM`및 파일에는 `PrivateKey` 인증서와 암호화 되지 않은 개인 키가 포함 되어 있습니다.

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
