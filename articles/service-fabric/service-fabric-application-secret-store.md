---
title: Azure Service Fabric 중앙 비밀 저장소
description: 이 문서에서는 Azure Service Fabric에서 중앙 비밀 저장소를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869758"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric의 중앙 비밀 저장소 
이 문서에서는 Azure Service Fabric에서 CSS(중앙 비밀 저장소)를 사용하여 Service Fabric 애플리케이션에서 비밀을 만드는 방법을 설명합니다. CSS는 중요한 데이터(예: 암호, 토큰, 키 등)를 메모리에 암호화된 상태로 유지하는 로컬 비밀 저장소 캐시입니다.

  > [!NOTE] 
  > SF 버전 7.1 이전에 CSS를 처음 활성화할 때 CU3는 Windows 인증 클러스터에서 CSS가 활성화 된 경우 활성화가 실패 하 고 CSS가 영구적 비정상 상태로 유지 될 수 있습니다. CSS가 모든 클러스터에서 활성화 되어 있지만 `EncryptionCertificateThumbprint` 올바르게 선언 되지 않았거나 해당 인증서가 설치 되지 않았거나 노드에 ACL로 설정 되어 있지 않습니다. Windows 인증 클러스터의 경우 7.1을 참조 하세요. 계속 하기 전에 CU3. 다른 클러스터의 경우 이러한 고정을 다시 확인 하거나 7.1에 제공 하세요. CU3.
  
## <a name="enable-central-secrets-store"></a>중앙 비밀 저장소 사용
CSS를 사용하도록 설정하려면 `fabricSettings` 아래의 클러스터 구성에 다음 스크립트를 추가합니다. CSS에 대해 클러스터 인증서 이외의 인증서를 사용하는 것이 좋습니다. 암호화 인증서가 모든 노드에 설치되어 있고 `NetworkService`에 인증서의 프라이빗 키에 대한 읽기 권한이 있는지 확인합니다.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>비밀 리소스 선언
REST API를 사용하여 비밀 리소스를 만들 수 있습니다.
  > [!NOTE] 
  > 클러스터가 windows 인증을 사용하는 경우 REST 요청은 보안되지 않은 HTTP 채널을 통해 전송됩니다. 보안 엔드포인트가 포함된 X509 기반 클러스터를 사용하는 것이 좋습니다.

REST API를 사용하여 `supersecret` 비밀 리소스를 만들려면 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`에 PUT 요청을 만듭니다. 비밀 리소스를 만들려면 클러스터 인증서 또는 관리자 클라이언트 인증서가 필요합니다.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>비밀 값 설정

다음 스크립트를 사용하여 비밀 값을 설정하도록 REST API를 사용합니다.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>비밀 값 검사
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>애플리케이션에서 비밀 사용

Service Fabric 애플리케이션에서 비밀을 사용하려면 다음 단계를 수행합니다.

1. 다음 코드 조각을 사용하여 **settings.xml** 파일에 섹션을 추가합니다. 이 값은 {`secretname:version`} 형식으로 되어 있습니다.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. **ApplicationManifest.xml**에 있는 섹션을 가져옵니다.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   환경 변수 `SecretPath`는 모든 비밀이 저장된 디렉터리를 가리킵니다. `testsecrets` 섹션 아래에 나열된 각 매개 변수는 별도의 파일에 저장됩니다. 이제 애플리케이션은 다음과 같이 비밀을 사용할 수 있습니다.
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 컨테이너에 비밀을 탑재합니다. 컨테이너 내에서 비밀을 사용할 수 있도록 설정하는 데 필요한 유일한 변경 내용은 탑재 지점을 `<ConfigPackage>`에 `specify`하는 것입니다.
다음 코드 조각은 수정된 **ApplicationManifest.xml**입니다.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   컨테이너 내부의 탑재 지점에서 비밀을 사용할 수 있습니다.

1. `Type='SecretsStoreRef`를 지정하여 비밀을 프로세스 환경 변수에 바인딩할 수 있습니다. 다음 코드 조각은 `supersecret` 버전 `ver1`을 **ServiceManifest.xml**의 환경 변수 `MySuperSecret`에 바인딩하는 방법의 예제입니다.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>다음 단계
[애플리케이션 및 서비스 보안](service-fabric-application-and-service-security.md)에 대해 자세히 알아봅니다.
