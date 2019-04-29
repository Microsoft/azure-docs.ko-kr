---
title: Service Fabric 서비스 분할 | Microsoft Docs
description: Service Fabric 상태 저장 서비스를 분할하는 방법을 설명합니다. 파티션을 사용하면 로컬 머신에 데이터가 저장되므로 데이터와 계산을 함께 확장할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: atsenthi
ms.openlocfilehash: 833d87dab59890b9903ea8eecf2334d7dd1c7436
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711888"
---
# <a name="partition-service-fabric-reliable-services"></a>서비스 패브릭 Reliable Services 분할
이 문서에서는 Azure 서비스 패브릭 Reliable Services 분할의 기본 개념에 대한 소개를 제공합니다. 문서에 사용되는 소스 코드는 [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)에서도 확인할 수 있습니다.

## <a name="partitioning"></a>분할
분할은 서비스 패브릭에만 있는 것이 아닙니다. 사실, 분할은 확장 가능한 서비스 구축의 코어 패턴입니다. 광범위한 의미로 분할을 상태(데이터) 분할의 개념으로 생각하고 확장성 및 성능 향상을 위해 더 작은 액세스 가능한 단위로 컴퓨팅할 수 있습니다. 분할의 잘 알려진 양식은 [데이터 분할][wikipartition]로서 분할이라고도 합니다.

### <a name="partition-service-fabric-stateless-services"></a>서비스 패브릭 상태 비저장 분할 서비스
상태 비저장 서비스의 경우 하나 이상의 서비스의 인스턴스를 포함하는 논리 단위가 되는 파티션으로 생각할 수 있습니다. 그림 1에서는 하나의 파티션을 사용하는 클러스터에 분산된 5개의 인스턴스로 상태 비저장 서비스를 보여 줍니다.

![상태 비저장 서비스](./media/service-fabric-concepts-partitioning/statelessinstances.png)

실제로 두 종류의 상태 비저장 서비스 솔루션이 있습니다. 첫 번째는 Azure SQL 데이터베이스처럼 외부적으로 상태를 유지하는 서비스(세션 정보 및 데이터를 저장하는 웹 사이트처럼)입니다. 두 번째는 모든 영구적 상태를 관리하지 않는 계산 전용 서비스(예: 계산기 또는 이미지 미리 보기)입니다.

두 경우 모두 상태 비저장 서비스 분할은 매우 드문 시나리오이며 확장성 및 가용성은 일반적으로 더 많은 인스턴스를 추가하여 이루어집니다. 상태 비저장 서비스 인스턴스에 대한 여러 파티션을 고려하려는 경우는 특별한 라우팅 요청을 충족해야 하는 때입니다.

한 예로 특정 범위의 ID가 있는 사용자가 특정한 서비스 인스턴스로만 제공되어야 하는 경우 고려합니다. 상태 비저장 서비스를 분할할 수 있는 경우의 다른 예는 분할된 SQL 데이터베이스와 같은 실제로 분할된 백 엔드가 있는 경우와 데이터베이스 분할에 작성하거나 백 엔드에서 사용되므로 동일한 분할 정보가 필요한 상태 비저장 서비스 내에서 다른 준비 작업을 수행해야 하는 서비스 인스턴스를 제어하려는 경우입니다. 이러한 유형의 시나리오는 다른 방법으로 해결될 수 있으며 서비스 분할이 반드시 필요하지 않습니다.

이 연습의 나머지 부분에서는 상태 저장 서비스에 중점을 둡니다.

### <a name="partition-service-fabric-stateful-services"></a>서비스 패브릭 상태 저장 분할 서비스
Service Fabric은 파티션 상태(데이터)에 최상의 방법을 제공하여 확장 가능한 상태 저장 서비스를 쉽게 개발할 수 있습니다. 개념적으로 상태 저장 서비스의 파티션이란 클러스터의 노드에 배포 및 분산된 [복제본](service-fabric-availability-services.md) 을 통해 매우 안정적으로 배율이 조정되는 배율 단위로 생각할 수 있습니다.

서비스 패브릭 상태 저장 서비스의 컨텍스트에서 분할은 특정 서비스 파티션이 서비스 전체 상태의 부분을 담당하는 것을 결정하는 프로세스를 말합니다. (앞서 언급했듯이 파티션은 [복제본](service-fabric-availability-services.md)의 집합입니다.) 서비스 패브릭에 대한 흥미로운 사항은 다른 노드에 파티션을 배치하기 때문에 노드의 리소스 제한까지 확장이 가능하다는 점입니다. 데이터 요구 사항이 늘어나고 파티션이 늘어나면 서비스 패브릭이 노드 간에 파티션의 균형을 다시 조정합니다. 따라서 하드웨어 리소스가 계속해서 효율적으로 사용됩니다.

예를 들어 5 노드 클러스터와 10개의 파티션 및 세 복제본의 대상을 가지도록 구성된 서비스로 시작합니다. 이 경우 서비스 패브릭은 클러스터에 복제본을 분산 및 배포하고 노드당 2개의 기본 [복제본](service-fabric-availability-services.md) 이 발생합니다.
클러스터를 10개의 노드로 확장해야 하는 경우 서비스 패브릭은 10개의 모든 노드에 기본 [복제본](service-fabric-availability-services.md) 을 다시 분산합니다. 마찬가지로 5개의 노드로 다시 조정하는 경우 서비스 패브릭은 5개의 노드로 모든 복제본을 다시 분산합니다.  

그림 2는 클러스터 크기 조정 전과 후의 10개의 파티션 배포를 보여 줍니다.

![상태 저장 서비스](./media/service-fabric-concepts-partitioning/partitions.png)

결과적으로 클라이언트의 요청이 컴퓨터 간에 분산되므로 확장이 달성되고 애플리케이션의 전체 성능이 향상되며 데이터의 청크에 대한 액세스의 경합이 줄어듭니다.

## <a name="plan-for-partitioning"></a>분할에 대한 계획
서비스를 구현하기 전에 확장하는 데 필요한 분할 전략을 항상 고려해야 합니다. 여러 가지 방법이 있지만 모든 방법은 애플리케이션이 달성해야 하는 것에 집중합니다. 이 문서의 컨텍스트에 대해 몇 가지 더 중요한 측면을 생각해 봅시다.

첫 단계로 분할되어야 하는 상태의 구조에 대해 생각하는 것이 좋습니다.

간단한 예를 살펴봅시다. County 전체 설문 조사에 대 한 서비스를 빌드 하려는 경우에 군에 있는 각 도시에 대 한 파티션을 만들 수 있습니다. 그런 다음 그 도시에 해당하는 파티션에 있는 도시에 속한 모든 사람의 투표 결과를 저장할 수 있습니다. 그림 3은 사람과 거주하는 도시의 집합을 보여 줍니다.

![간단한 파티션](./media/service-fabric-concepts-partitioning/cities.png)

도시의 인구는 다르므로 많은 양의 데이터(예: 시애틀)를 포함하는 일부 파티션 및 매우 적은 상태(예: 커클랜드)의 다른 파티션이 될 수 있습니다. 따라서 상태의 균등하지 않은 양으로 파티션을 갖는 것의 영향을 무엇입니까?

예를 다시 생각해 본다면 시애틀에 대한 투표를 가진 파티션이 커클랜드의 파티션에 비해 더 많은 트래픽을 얻는 것을 쉽게 볼 수 있습니다. 기본적으로 서비스 패브릭은 각 노드의 기본 및 보조 복제본 수를 거의 비슷하게 유지하므로 노드의 어떤 복제본은 트래픽을 더 많이 처리하고 어떤 복제본은 트래픽을 더 적게 처리하게 됩니다. 클러스터에서 다음과 같이 핫(hot) 및 콜드(cold) 지점을 방지하고자 합니다.

이를 방지하기 위해 분할 관점에서 두 가지를 수행해야 합니다.

* 상태를 분할하도록 시도하여 모든 파티션에 균등하게 분산되도록 합니다.
* 서비스에 대한 각 복제본에서 부하를 보고합니다. (방법에 대한 자세한 정보는 이 문서의 [메트릭 및 부하](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요). 서비스 패브릭은 메모리의 양 또는 레코드 수와 같은 서비스에서 사용한 부하를 보고하는 기능을 제공합니다. 보고된 메트릭에 따라 서비스 패브릭은 일부 파티션이 다른 파티션보다 더 높은 부하를 처리하는 것을 감지하고 복제본을 더 적합한 노드로 이동하여 클러스터를 다시 분산하므로 전체적으로 어떤 노드도 오버로드되지 않습니다.

때때로 지정된 파티션에 얼마나 많은 데이터가 있는지 알 수 없는 경우가 있으므로 첫째로 파티션에 균등하게 데이터를 분산하는 분할 전략을 도입하고 둘째로 부하를 보고하여 모두를 실행하는 것이 좋습니다.  첫 번째 방법은 투표 예제에 설명된 상황을 예방하고 두 번째 방법은 시간이 지남에 따라 액세스 또는 부하의 임시적인 차이를 해결하도록 합니다.

파티션 계획의 다른 측면은 올바른 파티션 수로 시작하도록 선택하는 것입니다.
서비스 패브릭 관점에서 시나리오의 예상 파티션 수보다 더 많은 파티션으로 시작하지 못할 이유는 없습니다.
사실, 파티션 수를 최대로 늘리는 것도 유효한 방식입니다.

드물기는 하지만 처음에 선택한 것보다 더 많은 파티션이 필요하게 될 수도 있습니다. 사후에 파티션 수를 변경할 수 없으므로 서비스 유형이 동일한 새 서비스 인스턴스를 만드는 것처럼 보다 발전된 파티션 접근 방식을 적용해야 합니다. 또한 클라이언트 코드에서 유지해야 하는 클라이언트 쪽 기술 자료에 따라 요청을 올바른 서비스 인스턴스로 라우팅하는 클라이언트 쪽 논리를 구현해야 합니다.

분할 계획에 대해 고려해야 할 다른 사항은 사용 가능한 컴퓨터 리소스입니다. 상태는 액세스되고 저장되어야 하므로 다음이 적용됩니다.

* 네트워크 대역폭 제한
* 시스템 메모리 제한
* 디스크 저장소 제한

그렇다면 실행 중인 클러스터에 리소스 제약이 발생하는 경우 어떻게 됩니까? 단순히 새 요구 사항에 맞게 클러스터를 확장할 수 있습니다.

[용량 계획 가이드](service-fabric-capacity-planning.md)는 클러스터에서 필요한 노드 수를 결정하는 방법에 대한 지침을 제공합니다.

## <a name="get-started-with-partitioning"></a>분할 시작
이 섹션에는 서비스 분할을 시작하는 방법을 설명합니다.

Service Fabric은 세 가지 파티션 체계를 제공합니다.

* 범위 지정된 분할(UniformInt64Partition이라고도 함)
* 이름 지정된 분할. 일반적으로 이 모델을 사용한 애플리케이션에는 제한된 집합 내에 버킷 가능한 데이터가 있습니다. 이름 지정된 파티션 키로 사용되는 데이터 필드의 몇 가지 일반적인 예는 지역, 우편 번호, 고객 그룹 또는 기타 비즈니스 경계입니다.
* 단일 분할 단일 파티션은 서비스가 추가 라우팅이 필요하지 않은 경우에 일반적으로 사용됩니다. 예를 들어 상태 비저장 서비스는 기본적으로 이 파티션 체계를 사용합니다.

이름 지정된 분할 체계 및 단일 분할 체계는 범위 지정된 파티션의 특별한 형태입니다. 기본적으로 서비스 패브릭에 대한 Visual Studio 템플릿은 가장 일반적이고 유용한 것이므로 범위 지정된 분할을 사용합니다. 이 문서의 나머지 부분에서는 범위 지정된 파티션 체계에 중점을 둡니다.

### <a name="ranged-partitioning-scheme"></a>범위 지정된 분할 체계
정수 범위(하위 및 상위 키로 식별됨) 및 파티션의 수(n)를 지정하는 데 사용됩니다. 전체 파티션 키 범위의 겹치지 않는 하위 범위를 각각 담당하는 n 파티션을 만듭니다. 예를 들어 하위 키 0, 상위 키 99 및 숫자 4로 범위 지정된 분할 체계는 다음과 같이 4개의 파티션을 만듭니다.

![범위 분할](./media/service-fabric-concepts-partitioning/range-partitioning.png)

일반적인 방법은 데이터 집합 내에서 고유 키를 기반으로 해시를 만드는 것입니다. 키의 일부 일반적인 예로는 차량 식별 번호(VIN), 직원 ID 또는 고유 문자열을 들 수 있습니다. 고유 키를 사용하여 해시 코드를 생성하고 키 범위를 계수하여 키로 사용할 수 있습니다. 허용되는 키 범위의 상한 및 하한 범위를 지정할 수 있습니다.

### <a name="select-a-hash-algorithm"></a>해시 알고리즘 선택
해시의 중요한 부분은 해시 알고리즘을 선택하는 것입니다. 서로 가까운 유사한 키를 그룹화하는 것이 목적인지(집약성을 중요시하는 해시), 또는 더욱 일반적인 경우인 활동이 모든 파티션에 널리 배포되는지(배포 해시) 여부를 고려해야 합니다.

적절한 배포 해시 알고리즘의 특징은 계산하기 쉽고 충돌이 적고 키를 균등하게 배분하는 것입니다. 효율적인 해시 알고리즘의 좋은 예로 [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) 해시 알고리즘이 있습니다.

일반적인 해시 코드 알고리즘 선택에 대한 좋은 리소스는 [해시 기능에 대한 Wikipedia 페이지](https://en.wikipedia.org/wiki/Hash_function)입니다.

## <a name="build-a-stateful-service-with-multiple-partitions"></a>여러 파티션으로 상태 저장 서비스 구축
여러 파티션으로 첫 번째 신뢰할 수 있는 상태 저장 서비스를 만들어 보겠습니다. 이 예에서 동일한 파티션에 동일한 문자로 시작하는 모든 성을 저장하려는 매우 간단한 애플리케이션을 작성합니다.

모든 코드를 작성하기 전에 파티션 및 파티션 키에 대해 생각해야 합니다. 알파벳의 각 문자로 26개의 파티션이 필요하지만 하위 키 및 상위 키는 어떻게 해야 할까요?
문자 그대로 문자당 하나의 파티션을 가지려 하기 때문에 각 문자는 해당 키이므로 하위 키로 0을, 상위 키로 25를 사용할 수 있습니다.

> [!NOTE]
> 실제로는 균등하게 분산되지 않으므로 여기서는 간소화된 시나리오를 사용합니다. 문자 "S" 또는 "M"으로 시작하는 성이 "X" 또는 "Y"로 시작하는 성보다 더 많습니다.
> 
> 

1. **Visual Studio** > **파일** > **새로 만들기** > **프로젝트**를 엽니다.
2. **새 프로젝트** 대화 상자에서 Service Fabric 애플리케이션을 선택합니다.
3. "AlphabetPartitions" 프로젝트를 호출합니다.
4. **서비스 만들기** 대화 상자에서 **상태 저장** 서비스를 선택하고 이름을 "Alphabet.Processing"으로 지정합니다.
5. 파티션 수를 설정합니다. AlphabetPartitions 프로젝트에서 ApplicationPackageRoot 폴더에 있는 ApplicationManifest.xml 파일을 열고 아래와 같이 매개 변수 Processing_PartitionCount를 26으로 업데이트합니다.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    또한 아래와 같이 ApplicationManifest.xml에 있는 StatefulService 요소의 LowKey 및 HighKey 속성을 업데이트해야 합니다.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. 서비스에 액세스할 수 있게 만들려면 아래와 같이 Alphabet.Processing 서비스에 대한 ServiceManifest.xml(PackageRoot 폴더에 있음)의 엔드포인트 요소를 추가하여 포트의 엔드포인트를 엽니다.
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    이제 26 파티션으로 내부 엔드포인트를 수신하도록 서비스가 구성됩니다.
7. 다음으로 처리 클래스의 `CreateServiceReplicaListeners()` 메서드를 재정의해야 합니다.
   
   > [!NOTE]
   > 이 샘플의 경우 간단한 HttpCommunicationListener를 사용하고 있다고 가정합니다. Reliable Services 통신에 대한 자세한 내용은 [Reliable Services 통신 모델](service-fabric-reliable-services-communication.md)을 참조하세요.
   > 
   > 
8. 복제본이 수신하는 URL에 대한 권장 패턴은 `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`형식입니다.
    따라서 통신 수신기가 올바른 엔드포인트에서 이 패턴을 사용하여 수신하도록 구성해야 합니다.
   
    이 서비스의 여러 복제본은 동일한 컴퓨터에서 호스팅될 수 있으므로 이 주소가 복제본에 고유해야 합니다. 이 때문에 URL에 파티션 ID와 복제본 ID가 있습니다. HttpListener는 URL 접두사가 고유하기만 하면 동일한 포트에서 여러 주소를 수신할 수 있습니다.
   
    추가 GUID는 보조 복제본이 읽기 전용 요청을 수신하는 고급 사례에 대해 사용되고 있습니다. 이 경우 클라이언트가 주소를 다시 확인하도록 기본에서 보조로 전환하는 경우 고유한 새 주소가 사용되도록 확인하려고 합니다. 여기서 '+'는 IP, FQDN, localhost 등의 사용 가능한 모든 호스트에서 복제본이 수신 대기할 수 있도록 주소로 사용됩니다. 아래 코드는 예제를 보여 줍니다.
   
    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    게시된 URL은 수신 대기 URL 접두사와 약간 다릅니다.
    수신 대기 URL이 HttpListener에 제공됩니다. 게시된 URL은 서비스 검색에 사용되는 서비스 패브릭 이름 명명 서비스에 게시된 URL입니다. 클라이언트는 해당 검색 서비스를 통해 이 주소에 대해 요청합니다. 클라이언트가 가져오는 주소에 연결하려면 노드의 실제 IP 또는 FQDN이 필요합니다. 따라서 아래와 같이 '+'를 노드의 IP 또는 FQDN으로 바꿔야 합니다.
9. 마지막 단계는 아래와 같이 서비스에 처리 논리를 추가하는 것입니다.
   
    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`는 파티션을 호출하는 데 사용되는 쿼리 문자열 매개 변수의 값을 읽고 신뢰할 수 있는 사전 `dictionary`에 성을 추가하도록 `AddUserAsync`를 호출합니다.
10. 특정 파티션을 호출할 수 있는 방법을 보도록 프로젝트에 상태 비저장 서비스를 추가하겠습니다.
    
    이 서비스는 쿼리 문자열 매개 변수로 성을 수락하고 파티션 키를 결정하고 처리를 위해 Alphabet.Processing에 이를 보내는 간단한 웹 인터페이스로 제공됩니다.
11. **서비스 만들기** 대화 상자에서 **상태 비저장** 서비스를 선택하고 아래와 같이 “Alphabet.Web”으로 지정합니다.
    
    ![상태 비저장 서비스 스크린 샷](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Alphabet.WebApi 서비스의 ServiceManifest.xml에서 엔드포인트 정보를 업데이트하여 아래와 같이 포트를 엽니다.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Web 클래스의 ServiceInstanceListeners 컬렉션을 반환해야 합니다. 다시 간단한 HttpCommunicationListener를 구현하도록 선택할 수 있습니다.
    
    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. 이제 처리 논리를 구현해야 합니다. HttpCommunicationListener는 요청이 들어올 때 `ProcessInputRequest` 를 호출합니다. 따라서 계속해서 아래 코드를 추가합니다.
    
    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    단계 별로 안내하겠습니다. 코드는 쿼리 문자열 매개 변수 `lastname` 의 첫 글자를 char로 읽습니다. 그런 다음 성의 첫 글자에 있는 16진수 값에서 `A` 의 16진수 값을 빼서 이 글자에 대한 파티션 키를 결정합니다.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    이 예제에서 파티션당 하나의 파티션 키를 가진 26개의 파티션을 사용합니다.
    그런 다음 `servicePartitionResolver` 개체의 `ResolveAsync` 메서드를 사용하여 이 키에 대한 서비스 파티션 `partition`을 가져옵니다. `servicePartitionResolver` 는 다음으로 정의됩니다.
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync` 메서드는 매개 변수로 서비스 URI, 파티션 키 및 취소 토큰을 사용합니다. 처리 서비스의 서비스 URI는 `fabric:/AlphabetPartitions/Processing`입니다. 그런 다음 파티션의 엔드포인트를 가져옵니다.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    마지막으로 엔드포인트 URL 및 쿼리 문자열을 작성하고 처리 서비스를 호출합니다.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    처리가 완료되면 출력을 다시 작성합니다.
15. 마지막 단계는 서비스를 테스트하는 것입니다. Visual Studio에서는 로컬 및 클라우드 배포에 대해 애플리케이션 매개 변수를 사용합니다. 26개의 파티션이 있는 서비스를 로컬에서 테스트하려면 아래와 같이 AlphabetPartitions 프로젝트의 ApplicationParameters 폴더에 있는 `Local.xml` 파일을 업데이트해야 합니다.
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. 배포가 완료되면 서비스 패브릭 탐색기에서 서비스 및 모든 해당 파티션을 확인할 수 있습니다.
    
    ![서비스 패브릭 탐색기 스크린 샷](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. 브라우저에서 `http://localhost:8081/?lastname=somename`을 입력하여 분할 논리를 테스트할 수 있습니다. 동일한 문자로 시작하는 각 성이 동일한 파티션에 저장되는 것을 확인할 수 있습니다.
    
    ![브라우저 스크린 샷](./media/service-fabric-concepts-partitioning/samplerunning.png)

샘플의 전체 소스 코드는 [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)에서 확인할 수 있습니다.

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services 및 작업자 포크 하위 프로세스
Service Fabric은 신뢰할 수 있는 서비스 및 이후의 신뢰할 수 있는 작업자 포크 하위 프로세스를 지원하지 않습니다. [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet)를 사용하여 지원되지 않는 하위 프로세스를 등록할 수는 없는데, 취소 토큰은 등록된 프로세스로만 전송되므로 상위 프로세스가 취소 토큰을 수신한 후 하위 프로세스가 닫히지 않으면 업그레이드 오류 등의 많은 문제가 발생하기 때문입니다. 

## <a name="next-steps"></a>다음 단계
서비스 패브릭 개념에 대한 자세한 내용은 다음을 참조하십시오.

* [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
* [서비스 패브릭 서비스의 확장성](service-fabric-concepts-scalability.md)
* [Service Fabric 애플리케이션의 용량 계획](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
