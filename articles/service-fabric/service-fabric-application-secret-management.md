---
title: Azure Service Fabric 애플리케이션 암호 관리 | Microsoft Docs
description: Service Fabric 애플리케이션에서 비밀 값을 보호하는 방법에 대해 설명합니다(플랫폼에 관계 없음).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d151dbf20e68a2152e9d886a74e51786bb8fbfa6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614477"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Service Fabric 애플리케이션에서 암호화된 비밀 관리
이 가이드에서는 Service Fabric 애플리케이션에서 비밀을 관리하는 단계를 안내합니다. 저장소 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다.

Service Fabric 애플리케이션에서 암호화된 비밀을 사용하는 데에는 세 가지 단계가 포함됩니다.
* 암호화 인증서 설정 및 비밀 암호화
* 애플리케이션에서 암호화된 비밀 지정
* 서비스 코드에서 암호화된 비밀 해독

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>암호화 인증서 설정 및 비밀 암호화
암호화 인증서를 설정하고 이를 사용하여 비밀을 암호화하는 것은 Windows와 Linux 간에 다릅니다.
* [Windows 클러스터에서 암호화 인증서 설정 및 비밀 암호화][secret-management-windows-specific-link]
* [Linux 클러스터에서 암호화 인증서 설정 및 비밀 암호화][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>애플리케이션에서 암호화된 비밀 지정
이전 단계에서 인증서를 사용하여 비밀을 암호화하고 애플리케이션에서 사용할 수 있도록 base-64로 인코딩된 문자열을 생성하는 방법을 설명했습니다. 이 base-64로 인코딩된 문자열을 서비스의 Settings.xml에서 암호화된 [매개 변수][parameters-link]로, 또는 서비스의 ServiceManifest.xml에서 암호화된 [환경 변수][environment-variables-link]로 지정할 수 있습니다.

서비스의 Settings.xml 구성 파일에서 암호화된 [매개 변수][parameters-link]를 `true`로 설정된 `IsEncrypted` 특성을 통해 지정합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
서비스의 ServiceManifest.xml 파일에서 암호화된 [환경 변수][environment-variables-link]를 `Encrypted`로 설정된 `Type` 특성을 통해 지정합니다.
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>애플리케이션 비밀을 애플리케이션 인스턴스에 삽입
여러 환경에 배포할 때에는 배포를 최대한 자동화하는 것이 좋습니다. 빌드 환경에서 비밀 암호화를 수행하고 애플리케이션 인스턴스를 만들 때 암호화된 비밀을 매개 변수로 제공하면 배포를 자동화할 수 있습니다.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Settings.xml에 재정의 가능한 매개 변수 사용
Settings.xml 구성 파일은 애플리케이션 생성 시 제공할 수 있는 재정의 가능한 매개 변수를 허용합니다. 매개 변수 값을 입력하는 대신 `MustOverride` 특성을 사용합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Settings.xml의 값을 재정의하려면 ApplicationManifest.xml에서 서비스의 재정의 매개 변수를 선언합니다.

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

이제 애플리케이션의 인스턴스를 만들 때 이 값을 *애플리케이션 매개 변수* 로 지정할 수 있습니다. 애플리케이션 인스턴스 만들기를 PowerShell로 스크립팅하거나 C#으로 작성하면 빌드 프로세스에 쉽게 통합할 수 있습니다.

PowerShell을 사용하면 매개 변수가 `New-ServiceFabricApplication` 명령에 [해시 테이블](https://technet.microsoft.com/library/ee692803.aspx)로 제공됩니다.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

C#을 사용하면 애플리케이션 매개 변수가 `ApplicationDescription`에 `NameValueCollection`로 지정됩니다.

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>서비스 코드에서 암호화된 비밀 해독
[매개 변수][parameters-link] 및 [환경 변수][environment-variables-link]에 액세스하기 위한 API로 암호화된 값을 손쉽게 해독할 수 있습니다. 암호화된 문자열에는 암호화에 사용된 인증서 정보가 포함되어 있으므로 수동으로 인증서를 지정할 필요가 없습니다. 서비스가 실행되고 있는 노드에 인증서를 설치하기만 하면 됩니다.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>다음 단계
[애플리케이션 및 서비스 보안](service-fabric-application-and-service-security.md)에 대한 자세한 정보

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
