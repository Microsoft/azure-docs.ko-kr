---
title: Azure Service Fabric DNS 서비스 | Microsoft Docs
description: Service Fabric의 DNS 서비스를 사용하여 클러스터 내부에서 마이크로 서비스를 검색할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: aljo
ms.openlocfilehash: 3b3262eadc732c23000a66f24aaeeed4d9794db0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947659"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric의 DNS 서비스
DNS 서비스는 DNS 프로토콜을 통해 다른 서비스를 검색하기 위해 클러스터에서 사용할 수 있는 선택적 시스템 서비스입니다. 

많은 서비스, 특히 컨테이너 서비스는 기존 URL을 통해 주소를 지정할 수 있습니다. Service Fabric Naming Service 프로토콜보다는 표준 DNS 프로토콜을 사용하여 이러한 서비스를 해결할 수 있어야 합니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 엔드포인트 IP 주소를 확인할 수 있습니다. 이러한 기능은 다양한 플랫폼에서 컨테이너 서비스의 이식성을 유지하며 명명 서비스를 활용할 수 있도록 코드를 다시 작성하기보다는 기존 서비스 URL을 사용할 수 있도록 하여 “리프트 앤 시프트”시나리오를 보다 쉽게 만들 수 있습니다. 

DNS 서비스는 DNS 이름을 서비스 이름에 매핑하며, 서비스 이름은 명명 서비스를 통해 확인되어 서비스 엔드포인트가 반환됩니다. 서비스의 DNS 이름은 생성 시 제공됩니다. 다음 다이어그램은 상태 비저장 서비스에 대해 DNS 서비스가 작동하는 방식을 보여 줍니다.

![서비스 엔드포인트](./media/service-fabric-dnsservice/stateless-dns.png)

Service Fabric 버전 6.3부터, 분할된 상태 저장 서비스 주소 지정 체계를 포함하도록 Service Fabric DNS 프로토콜이 확장되었습니다. 이러한 확장을 통해 상태 저장 서비스 DNS 이름과 파티션 이름을 조합하여 특정 파티션 IP 주소를 확인할 수 있습니다. 세 가지 파티션 구성표가 모두 지원됩니다.

- 이름 지정된 분할
- 범위 지정된 분할
- 단일 분할

다음 다이어그램은 분할된 상태 비저장 서비스에 대해 DNS 서비스가 작동하는 방식을 보여 줍니다.

![상태 저장 서비스 엔드포인트](./media/service-fabric-dnsservice/stateful-dns.png)

동적 포트는 DNS 서비스에서 지원되지 않습니다. 동적 포트에서 노출되는 서비스를 확인하려면 [역방향 프록시 서비스](./service-fabric-reverseproxy.md)를 사용합니다.

## <a name="enabling-the-dns-service"></a>DNS 서비스 사용
> [!NOTE]
> Service Fabric 서비스에 대한 DNS 서비스는 아직 Linux에서 지원되지 않습니다.

포털을 사용하여 클러스터를 만들 때 DNS 서비스는 **클러스터 구성** 메뉴의 **DNS 서비스 포함** 확인란에서 기본적으로 활성화됩니다.

![포털을 통해 DNS 서비스를 사용하도록 설정](./media/service-fabric-dnsservice/enable-dns-service.png)

클러스터를 만드는 데 포털을 사용하지 않거나 기존 클러스터를 업데이트하는 경우 템플릿에서 DNS 서비스를 활성화해야 합니다.

- 새 클러스터를 배포하기 위해 [샘플 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)을 사용하거나 사용자 고유의 Resource Manager 템플릿을 만들 수 있습니다. 
- 기존 클러스터를 업데이트하기 위해 포털에서 클러스터의 리소스 그룹으로 이동하고 **Automation 스크립트**를 클릭하여 클러스터의 현재 상태를 반영하는 템플릿 및 그룹의 다른 리소스를 사용하여 작업할 수 있습니다. 자세히 알아보려면 [리소스 그룹에서 템플릿 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)를 참조하세요.

템플릿을 가져온 후 다음 단계에 따라 DNS 서비스를 활성화할 수 있습니다.

1. 다음 예제와 같이 `Microsoft.ServiceFabric/clusters` 리소스에 대해 `apiversion`이 `2017-07-01-preview` 또는 이후로 설정되었는지 확인하고 이렇게 설정되어 있지 않으면 업데이트합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 이제 다음 방법 중 하나에서 DNS 서비스를 활성화합니다.

   - 기본 설정으로 DNS 서비스를 활성화하려면 다음 예와 같이 `properties` 섹션 안의 `addonFeatures` 섹션에 추가합니다.

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - 기본 설정 이외의 설정으로 서비스를 활성화하려면 `properties` 섹션의 `fabricSettings` 섹션 안에 `DnsService` 섹션을 추가합니다. 이 경우 `addonFeatures`에 DnsService를 추가할 필요가 없습니다. DNS 서비스에 대해 설정할 수 있는 속성에 대한 자세한 내용은 [DNS 서비스 설정](./service-fabric-cluster-fabric-settings.md#dnsservice)을 참조하세요.

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. 변경 사항으로 클러스터 템플릿을 업데이트한 후에는 이를 적용하여 업그레이드를 완료합니다. 업그레이드가 완료되면 DNS 시스템 서비스가 클러스터에서 실행을 시작합니다. 서비스 이름은 `fabric:/System/DnsService`이며, Service Fabric 탐색기의 **시스템** 서비스 섹션 아래에서 찾을 수 있습니다. 


## <a name="setting-the-dns-name-for-your-service"></a>서비스에 대한 DNS 이름 설정
ApplicationManifest.xml 파일에서 기본 서비스에 대해 선언적으로 또는 PowerShell 명령을 통해 서비스에 대한 DNS 이름을 설정할 수 있습니다.

서비스의 DNS 이름은 클러스터 전체에서 확인할 수 있으므로 클러스터에서 DNS 이름의 고유성을 확인하는 것이 중요합니다. 

`<ServiceDnsName>.<AppInstanceName>`의 이름 지정 체계를 사용하는 것이 좋습니다(예: `service1.application1`). 애플리케이션이 Docker Compose를 사용하여 배포되는 경우 서비스는 이 이름 지정 체계를 사용하여 DNS 이름이 자동으로 할당됩니다.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>ApplicationManifest.xml에서 기본 서비스에 대한 DNS 이름 설정
Visual Studio 또는 원하는 편집기에서 프로젝트를 연 다음 ApplicationManifest.xml 파일을 엽니다. 기본 서비스 섹션으로 이동한 다음 각 서비스에 대해 `ServiceDnsName` 특성을 추가합니다. 다음 예제에서는 서비스의 DNS 이름을 `service1.application1`로 설정하는 방법을 보여 줍니다.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
애플리케이션을 배포한 후 Service Fabric Explorer의 서비스 인스턴스에는 다음 그림에 나와 있는 것처럼 이 인스턴스에 대한 DNS 이름이 표시됩니다. 

![서비스 엔드포인트](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

다음 예제에서는 상태 저장 서비스에 대한 DNS 이름을 `statefulsvc.app`으로 설정합니다. 서비스는 이름 지정된 파티션 구성표를 사용합니다. 파티션 이름은 소문자입니다. 이것은 DNS 쿼리에서 대상이 될 파티션에 대한 요구 사항입니다. 자세한 내용은 [상태 저장 서비스 파티션에서 DNS 쿼리 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition)를 참조하세요.

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Powershell을 사용하여 서비스에 대한 DNS 이름 설정
`New-ServiceFabricService` Powershell 명령을 사용하여 만들 때 서비스에 대한 DNS 이름을 설정할 수 있습니다. 다음 예제에서는 DNS 이름 `service1.application1`을 사용하여 새로운 상태 비저장 서비스를 만듭니다.

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[미리 보기] 상태 저장 서비스 파티션에서 DNS 쿼리 만들기
Service Fabric 버전 6.3부터 Service Fabric DNS 서비스는 서비스 파티션에 대한 쿼리를 지원합니다.

DNS 쿼리에 사용될 파티션의 경우 다음과 같은 명명 제한이 적용됩니다.

   - 파티션 이름은 DNS와 호환되어야 합니다.
   - 이름에 점, '.'을 포함하는 다중 레이블 파티션 이름은 사용하지 않아야 합니다.
   - 파티션 이름은 소문자여야 합니다.

파티션을 대상으로 하는 DNS 쿼리 형식은 다음과 같습니다.

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
위치:

- *First-Label-Of-Partitioned-Service-DNSName*은 서비스 DNS 이름의 첫 번째 부분입니다.
- *PartitionPrefix*는 클러스터 매니페스트의 DnsService 섹션 또는 클러스터의 Resource Manager 템플릿을 통해 설정할 수 있는 값입니다. 기본값은 “-”입니다. 자세한 내용은 [DNS 서비스 설정](./service-fabric-cluster-fabric-settings.md#dnsservice)을 참조하세요.
- *Target-Partition-Name*은 파티션의 이름입니다. 
- *PartitionSuffix*는 클러스터 매니페스트의 DnsService 섹션 또는 클러스터의 Resource Manager 템플릿을 통해 설정할 수 있는 값입니다. 기본값은 빈 문자열입니다. 자세한 내용은 [DNS 서비스 설정](./service-fabric-cluster-fabric-settings.md#dnsservice)을 참조하세요.
- *Remaining-Partitioned-Service-DNSName*은 서비스 DNS 이름의 나머지 부분입니다.

다음 예제는 `PartitionPrefix` 및 `PartitionSuffix`에 대한 기본 설정이 있는 클러스터에서 실행 중인 분할된 서비스에 대한 DNS 쿼리를 보여 줍니다. 

- 범위 지정된 분할 체계를 사용하는 DNS 이름이 `backendrangedschemesvc.application`인 서비스의 “0” 파티션을 확인하려면 `backendrangedschemesvc-0.application`를 사용합니다.
- 이름 지정된 분할 체계를 사용하는 DNS 이름이 `backendnamedschemesvc.application`인 서비스의 “first” 파티션을 확인하려면 `backendnamedschemesvc-first.application`를 사용합니다.

DNS 서비스는 파티션의 기본 복제본의 IP 주소를 반환합니다. 파티션을 지정하지 않으면 서비스는 무작위로 선택된 파티션의 주 복제본의 IP 주소를 반환합니다.

## <a name="using-dns-in-your-services"></a>서비스에 DNS 사용
둘 이상의 서비스를 배포하는 경우 DNS 이름을 사용하여 통신할 다른 서비스의 엔드포인트를 찾을 수 있습니다. DNS 서비스는 상태 비저장 서비스와 Service Fabric 버전 6.3 이상에서 상태 저장 서비스에 대해 작동합니다. Service Fabric 6.3 이전 버전에서 실행 중인 상태 저장 서비스의 경우 http 호출에 기본 제공 [역방향 프록시 서비스](./service-fabric-reverseproxy.md)를 사용하여 특정 서비스 파티션을 호출할 수 있습니다. 

동적 포트는 DNS 서비스에서 지원되지 않습니다. 역방향 프록시 서비스를 사용하여 동적 포트를 사용하는 서비스를 확인할 수 있습니다.

다음 코드에는 DNS 통해 상태 비저장 서비스를 호출하는 방법을 보여 줍니다. URL의 일부분으로 DNS 이름, 포트와 선택적 경로를 제공하는 간단한 일반 http 호출입니다.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

다음 코드는 상태 저장 서비스의 특정 파티션에서 호출을 보여 줍니다. 이 경우 DNS 이름은 파티션 이름(partition1)을 포함합니다. 이 호출에서는 `PartitionPrefix` 및 `PartitionSuffix`에 대한 기본값이 있는 클러스터를 가정합니다.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>알려진 문제
* Service Fabric 버전 6.3 이상에서는 DNS 이름에 하이픈이 포함된 서비스 이름에 대한 DNS 조회에 문제가 있습니다. 자세한 내용은 이 [GitHub 문제](https://github.com/Azure/service-fabric-issues/issues/1197)를 추적하세요. 이 문제에 대한 수정은 다음 6.3 업데이트에서 제공될 예정입니다. 

* Service Fabric 서비스에 대한 DNS 서비스는 아직 Linux에서 지원되지 않습니다. DNS 서비스는 Linux의 컨테이너에 대해 지원되지 않습니다. Fabric Client/ServicePartitionResolver를 사용한 수동 해결은 대체 수단으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[서비스와 연결 및 통신](service-fabric-connect-and-communicate-with-services.md)에서 클러스터 내의 서비스 통신에 대해 자세히 알아봅니다.

