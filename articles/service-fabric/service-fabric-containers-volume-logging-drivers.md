---
title: Service Fabric용 Azure Files 볼륨 드라이버
description: Service Fabric은 컨테이너에서 볼륨을 백업하도록 Azure Files 사용을 지원합니다.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: d3acb20723bc826a120a8333c0ef63c33131376a
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901208"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Service Fabric용 Azure Files 볼륨 드라이버

Azure Files 볼륨 드라이버는 Docker 컨테이너에 사용할 [Azure Files](../storage/files/storage-files-introduction.md) 기반 볼륨을 제공하는 [Docker 볼륨 플러그 인](https://docs.docker.com/engine/extend/plugins_volume/)입니다. 클러스터 내 다른 Service Fabric 컨테이너 애플리케이션에 사용할 볼륨을 제공하도록 Service Fabric 클러스터에 배포할 수 있는 Service Fabric 애플리케이션으로 패키지됩니다.

> [!NOTE]
> Azure Files 볼륨 플러그 인 버전 6.5.661.9590이 일반 공급으로 출시되었습니다.
>

## <a name="prerequisites"></a>사전 요구 사항
* Windows 버전의 Azure Files 볼륨 플러그 인은 [Windows Server 버전 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 버전 1709](/windows/whats-new/whats-new-windows-10-version-1709) 이상 운영 체제에서만 작동합니다.

* Linux 버전의 Azure Files 볼륨 플러그 인은 Service Fabric에서 지원하는 모든 운영 체제 버전에서 작동합니다.

* Azure Files 볼륨 플러그 인은 Service Fabric 버전 6.2 이상에서만 작동합니다.

* [Azure Files 설명서](../storage/files/storage-how-to-create-file-share.md)의 지침을 따라 볼륨으로 사용할 Service Fabric 컨테이너 애플리케이션에 대한 파일 공유를 만듭니다.

* [Service Fabric 모듈을 사용하는 Powershell](./service-fabric-get-started.md) 또는 [SFCTL](./service-fabric-cli.md) 설치가 필요합니다.

* Hyper-V 컨테이너를 사용하는 경우 다음 코드 조각을 ClusterManifest(로컬 클러스터), Azure Resource Manager 템플릿의 fabricSettings 섹션(Azure 클러스터) 또는 ClusterConfig.json(독립 실행형 클러스터)에 추가해야 합니다.

ClusterManifest의 Hosting 섹션에 다음을 추가해야 합니다. 이 예제에서는 볼륨 이름이 **sfazurefile** 이고 클러스터의 수신 대기 포트가 **19100** 입니다. 해당 항목을 클러스터에 맞는 값으로 바꿉니다.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Azure Resource Manager 템플릿의 fabricSettings 섹션(Azure 배포의 경우) 또는 ClusterConfig.json(독립 실행형 배포의 경우)에서 다음 코드 조각을 추가해야 합니다. 여기에서도 볼륨 이름 및 포트 값은 사용자의 고유한 값으로 바꿉니다.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Service Fabric Azure Files 볼륨 드라이버를 사용하여 샘플 애플리케이션 배포

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>제공된 Powershell 스크립트를 통해 Azure Resource Manager 사용(권장)

클러스터가 Azure를 기반으로 하는 경우 사용 편의성을 위해 Azure Resource Manager 애플리케이션 리소스 모델을 사용하여 애플리케이션을 배포하는 것이 좋습니다. 이렇게 하면 인프라를 코드로 유지 관리하는 모델로 전환하는 데 도움이 됩니다. 이 방법을 사용하면 Azure Files 볼륨 드라이버의 앱 버전을 추적하지 않아도 됩니다. 지원되는 OS마다 별도의 Azure Resource Manager 템플릿을 유지 관리할 수도 있습니다. 이 스크립트에서는 최신 버전의 Azure Files 애플리케이션을 배포하며 OS 유형, 클러스터 구독 ID, 리소스 그룹에 대한 매개 변수를 사용한다고 가정합니다. 스크립트는 [Service Fabric 다운로드 사이트](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)에서 다운로드할 수 있습니다. Azure Files 볼륨 플러그 인이 Docker 디먼의 요청을 수신 대기하는 포트인 ListenPort는 자동으로 19100으로 설정됩니다. “listenPort”라는 매개 변수를 추가하여 변경할 수 있습니다. 이 포트가 클러스터 또는 애플리케이션에서 사용하는 다른 포트와 충돌하지 않는지 확인합니다.
 

Windows용 Azure Resource Manager 배포 명령은 다음과 같습니다.
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Linux용 Azure Resource Manager 배포 명령은 다음과 같습니다.
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

스크립트를 성공적으로 실행하면 [애플리케이션 구성 섹션](#configure-your-applications-to-use-the-volume)으로 건너뛸 수 있습니다.


### <a name="manual-deployment-for-standalone-clusters"></a>독립 실행형 클러스터 수동 배포

컨테이너용 볼륨을 제공하는 Service Fabric 애플리케이션은 [Service Fabric 다운로드 사이트](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip)에서 다운로드할 수 있습니다. 애플리케이션은 [PowerShell](./service-fabric-deploy-remove-applications.md), [CLI](./service-fabric-application-lifecycle-sfctl.md) 또는 [FabricClient API](./service-fabric-deploy-remove-applications-fabricclient.md)를 통해 클러스터에 배포될 수 있습니다.

1. 명령줄을 사용하여 다운로드한 애플리케이션 패키지의 루트 디렉터리로 디렉터리를 변경합니다.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. 그런 다음, [ApplicationPackagePath] 및 [ImageStoreConnectionString]에 적절한 값을 사용하여 Image Store에 애플리케이션 패키지를 복사합니다.

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. 애플리케이션 형식 등록

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. **ListenPort** 애플리케이션 매개 변수 값에 유의하여 애플리케이션을 만듭니다. 이 값은 Azure Files 볼륨 플러그 인이 Docker 디먼의 요청을 수신 대기하는 포트입니다. 애플리케이션에 제공된 포트가 ClusterManifest의 VolumePluginPorts와 일치하고 클러스터 또는 애플리케이션에서 사용하는 다른 포트와 충돌하지 않는지 확인합니다.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter는 커넥터에 SMB 마운트를 매핑하도록 지원하지 않습니다([Windows Server 버전 1709에서만 지원됨](/virtualization/windowscontainers/manage-containers/container-storage)). 이러한 제약으로 인해 1709보다 오래된 버전에서 네트워크 볼륨 매핑 및 Azure Files 볼륨 드라이버를 사용할 수 없습니다.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>로컬 개발 클러스터에서 애플리케이션 배포
[위의](#manual-deployment-for-standalone-clusters) 1-3단계를 수행합니다.

 Azure Files 볼륨 플러그 인 애플리케이션에 대한 기본 서비스 인스턴스 수는 -1로, 클러스터의 각 노드에 배포된 서비스의 인스턴스가 있다는 것을 의미합니다. 그러나 로컬 개발 클러스터에서 Azure Files 볼륨 플러그 인 애플리케이션을 배포할 때 서비스 인스턴스 수는 1로 지정되어야 합니다. **InstanceCount** 애플리케이션 매개 변수를 통해 이를 수행할 수 있습니다. 따라서 로컬 개발 클러스터에서 Azure Files 볼륨 플러그 인 애플리케이션을 만드는 명령은 다음과 같습니다.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>볼륨을 사용할 애플리케이션 구성
다음 코드 조각에서는 애플리케이션의 애플리케이션 매니페스트 파일에서 Azure Files 기반 볼륨을 지정하는 방법을 보여줍니다. 관심 있는 특정 요소는 **볼륨** 태그입니다.

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure Files 볼륨 플러그 인에 대한 드라이버 이름은 **sfazurefile** 입니다. 이 값은 애플리케이션 매니페스트에서 **Volume** 태그 요소의 **Driver** 특성에 대해 설정됩니다.

위에 있는 코드 조각의 **Volume** 태그에서 Azure Files 볼륨 플러그 인에는 다음 특성이 있어야 합니다.
- **Source** - 볼륨의 이름입니다. 사용자는 해당 볼륨에 대한 이름을 임의로 선택할 수 있습니다.
- **Destination** - 이 특성은 실행 중인 컨테이너에서 볼륨이 매핑되는 위치입니다. 따라서 대상은 컨테이너 내에 이미 존재하는 위치가 될 수 없습니다.

위의 코드 조각의 **DriverOption** 요소에 나와 있는 것처럼 Azure Files 볼륨 플러그 인은 다음 드라이버 옵션을 지원합니다.
- **shareName** - 컨테이너에 대한 볼륨을 제공하는 Azure Files 파일 공유의 이름입니다.
- **storageAccountName** - Azure Files 파일 공유를 포함하는 Azure Storage 계정의 이름입니다.
- **storageAccountKey** - Azure Files 파일 공유를 포함하는 Azure Storage 계정에 대한 액세스 키입니다.
- **storageAccountFQDN** - 스토리지 계정과 연결된 도메인 이름입니다. storageAccountFQDN을 지정하지 않으면 storageAccountName과 함께 기본 접미사(file.core.windows.net)를 사용하여 도메인 이름이 생성됩니다.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>사용자 고유의 볼륨 또는 로깅 드라이버 사용
Service Fabric은 사용자 고유의 사용자 지정 [볼륨](https://docs.docker.com/engine/extend/plugins_volume/) 또는 [로깅](https://docs.docker.com/engine/admin/logging/overview/) 드라이버 사용을 허용합니다. Docker 볼륨/로깅 드라이버가 클러스터에 설치되어 있지 않으면 RDP/SSH 프로토콜을 사용하여 수동으로 설치할 수 있습니다. [가상 머신 확장 집합 시작 스크립트](https://azure.microsoft.com/resources/templates/vmss-custom-script-windows/) 또는 [SetupEntryPoint 스크립트](./service-fabric-application-model.md)를 통해 이러한 프로토콜을 사용하여 설치를 수행할 수 있습니다.

[Azure용 Docker 볼륨 드라이버](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)를 설치하는 스크립트의 예는 다음과 같습니다.

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

애플리케이션에서 설치한 볼륨 또는 로깅 드라이버를 사용하려면 애플리케이션 매니페스트에서 **ContainerHostPolicies** 아래의 **Volume** 및 **LogConfig** 요소에 적절한 값을 지정해야 합니다.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

볼륨 플러그 인을 지정할 때 Service Fabric은 지정된 매개 변수를 사용하여 볼륨을 자동으로 만듭니다. **Volume** 요소에 대한 **Source** 태그는 볼륨의 이름이며 **Driver** 태그는 볼륨 드라이버 플러그 인을 지정합니다. **Destination** 태그는 실행 중인 컨테이너에서 **Source** 가 매핑되는 위치입니다. 따라서 대상은 컨테이너 내에 이미 존재하는 위치가 될 수 없습니다. 옵션은 **DriverOption** 태그를 사용하여 다음과 같이 지정할 수 있습니다.

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

앞의 매니페스트 조각에 표시된 대로, 볼륨에 대해 애플리케이션 매개 변수가 지원됩니다(예제 사용에 대해서는 `MyStorageVar` 확인).

Docker 로그 드라이버가 지정된 경우 클러스터의 로그를 처리할 에이전트(또는 컨테이너)를 배포해야 합니다. **DriverOption** 태그는 로그 드라이버에 대한 옵션을 지정하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 볼륨 드라이버를 포함하여 컨테이너 샘플을 보려면 [Service Fabric 컨테이너 샘플](https://github.com/Azure-Samples/service-fabric-containers)을 참조하세요.
* 컨테이너를 Service Fabric 클러스터에 배포하려면 [Service Fabric에 컨테이너 배포](./service-fabric-get-started-containers.md) 문서를 참조하세요.
