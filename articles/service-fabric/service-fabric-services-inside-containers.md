---
title: Windows에서 Azure Service Fabric 서비스 컨테이너화
description: Windows에서 Service Fabric Reliable Services 및 Reliable Actors 서비스를 컨테이너화하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: aljo, anmola
ms.openlocfilehash: 147607bbea65199ff97459711ad6301a4ae93aa4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837524"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Windows에서 Service Fabric Reliable Services 및 Reliable Actors 컨테이너화

Service Fabric은 Service Fabric 마이크로 서비스(Reliable Services 및 Reliable Actor 기반 서비스)를 컨테이너화하도록 지원합니다. 자세한 내용은 [Service Fabric 컨테이너](service-fabric-containers-overview.md)를 참조하세요.

이 문서에서는 Windows 컨테이너 내부에서 실행 중인 서비스를 가져오기 위한 지침을 제공합니다.

> [!NOTE]
> 현재 이 기능은 Windows에서만 작동합니다. 컨테이너를 실행하려면 컨테이너가 포함된 Windows Server 2016에서 클러스터를 실행해야 합니다.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Service Fabric 애플리케이션을 컨테이너화하는 단계

1. Visual Studio에서 Service Fabric 애플리케이션을 엽니다.

2. 프로젝트에 [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) 클래스를 추가합니다. 이 클래스의 코드는 애플리케이션 내에 있는 Service Fabric 런타임 이진 파일을 컨테이너 내부에서 실행할 경우 바르게 로드하는 도우미입니다.

3. 컨테이너화하려는 각 코드 패키지에 대해 프로그램 진입점에 로더를 초기화합니다. 다음 코드 조각에 표시된 정적 생성자를 프로그램 진입점 파일에 추가합니다.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. 프로젝트를 빌드 및 [패키지](service-fabric-package-apps.md#Package-App)합니다. 패키지를 빌드하고 만들려면 솔루션 탐색기에서 애플리케이션 프로젝트를 마우스 오른쪽 단추로 클릭하고 **패키지** 명령을 선택합니다.

5. 컨테이너화해야 하는 모든 코드 패키지에서 [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1) PowerShell 스크립트를 실행합니다. 사용법은 다음과 같습니다.

    정식 .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      스크립트는 $dockerPackageOutputDirectoryPath에서 Docker 아티팩트를 포함한 폴더를 만듭니다. 포트를 `expose`하도록 생성된 Dockerfile을 수정하고, 설치 스크립트 등을 실행합니다. 사용자의 요구 사항을 기반으로 합니다.

6. 다음으로 Docker 컨테이너 패키지를 [빌드](service-fabric-get-started-containers.md#Build-Containers)하고 리포지토리에 [푸시](service-fabric-get-started-containers.md#Push-Containers)해야 합니다.

7. 컨테이너 이미지, 리포지토리 정보, 레지스트리 인증 및 포트와 호스트 간 매핑을 추가하도록 ApplicationManifest.xml 및 ServiceManifest.xml을 수정합니다. 매니페스트를 수정하기 위해 [Azure Service Fabric 컨테이너 애플리케이션 만들기](service-fabric-get-started-containers.md)를 참조하세요. 서비스 매니페스트의 코드 패키지 정의는 해당 컨테이너 이미지로 바꿔야 합니다. EntryPoint를 ContainerHost 형식으로 변경해야 합니다.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. 복제기 및 서비스 엔드포인트에 포트와 호스트 간 매핑을 추가합니다. 런타임 시 Service Fabric에서 이러한 포트를 모두 할당하기 때문에 ContainerPort는 매핑에 할당된 포트를 사용하도록 0으로 설정됩니다.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. 컨테이너 격리 모드 구성에 대해 알아보려면 [격리 모드 구성]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode)을 참조하세요. Windows는 컨테이너, 즉 프로세스 및 Hyper-V에 대한 두 가지 격리 모드를 지원합니다. 다음 코드 조각은 격리 모드가 애플리케이션 매니페스트 파일에서 지정되는 방법을 보여 줍니다.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

10. 이 애플리케이션을 테스트하려면 5.7 이상 버전을 실행하는 클러스터에 배포해야 합니다. 런타임 버전 6.1 이하의 경우에는 클러스터 설정을 편집하고 업데이트하여 이 미리 보기 기능을 사용하도록 설정해야 합니다. 이 [문서](service-fabric-cluster-fabric-settings.md)의 단계에 따라 다음에 표시된 설정을 추가합니다.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. 다음으로 이 클러스터에 편집된 애플리케이션 패키지를 [배포](service-fabric-deploy-remove-applications.md)합니다.

이제 컨테이너화된 Service Fabric 애플리케이션이 클러스터를 실행해야 합니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric의 컨테이너](service-fabric-get-started-containers.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* Service Fabric [애플리케이션 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
