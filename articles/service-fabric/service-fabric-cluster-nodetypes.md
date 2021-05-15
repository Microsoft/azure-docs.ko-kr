---
title: 노드 유형과 가상 머신 확장 집합
description: 가상 머신 확장 집합과 Azure Service Fabric 노드 유형이 어떤 관련이 있으며 확장 집합 인스턴스 또는 클러스터를 원격으로 연결하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: 9e30c02de54806006a1881448bcb9f788a57310c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97095256"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 노드 형식 및 가상 머신 확장 집합

[가상 머신 확장 집합](../virtual-machine-scale-sets/index.yml)은 Azure 컴퓨팅 리소스입니다. 확장 집합을 사용하여 가상 머신 컬렉션을 배포 및 관리할 수 있습니다. Azure Service Fabric 클러스터에서 정의한 각 노드 형식은 정확히 하나의 확장 집합을 설정합니다. 여러 노드 형식은 동일한 확장 집합으로 지원될 수 없으며 하나의 노드 형식을 여러 확장 집합에서 지원해서도 안 됩니다.

Service Fabric 런타임은 *Microsoft.Azure.ServiceFabric* 가상 머신 확장에 의해 확장 집합의 각 가상 머신에 설치됩니다. 각 노드 형식을 독립적으로 확장 또는 축소하고, 각 클러스터 노드에서 실행되는 OS SKU를 변경하고, 다른 포트의 집합을 열고, 다른 용량 메트릭을 사용할 수 있습니다.

다음 그림에서는 이름이 *FrontEnd* 및 *BackEnd* 인 두 노드 형식이 있는 클러스터를 보여줍니다. 각 노드 형식에는 5개의 노드가 있습니다.

![두 가지 노드 유형이 있는 클러스터][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>노드에 가상 머신 확장 집합 인스턴스 매핑

위의 그림에서처럼 확장 집합 인스턴스는 인스턴스 0에서 시작한 다음 1씩 증가합니다. 노드 이름에 이러한 번호 매기기가 반영됩니다. 예를 들어 노드 BackEnd_0은BackEnd 확장 집합의 인스턴스 0입니다. 이 특정 VM 확장 집합에는 이름이 BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 및 BackEnd_4인 5개의 인스턴스가 있습니다.

확장 집합을 확대하는 경우 새 인스턴스가 생성됩니다. 새 확장 집합 인스턴스 이름은 일반적으로 확장 집합 이름 + 다음 인스턴스 번호입니다. 이 예제에서는 BackEnd_5입니다.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>확장 집합 부하 분산 장치를 노드 형식 및 확장 집합에 매핑

Azure Portal에 클러스터를 배포했거나 샘플 Azure Resource Manager 템플릿을 사용한 경우 리소스 그룹의 모든 리소스가 목록에 열거됩니다. 각 확장 집합 또는 노드 형식에 대해 부하 분산 장치를 확인할 수 있습니다. 부하 분산 장치 이름은 **LB-&lt;노드 형식 이름&gt;** 형식을 사용합니다. 다음 그림에서처럼 LB-sfcluster4doc-0을 예로 들 수 있습니다.

![스크린샷은 두 개의 부하 분산 장치가 강조 표시된 리소스 그룹을 보여 줍니다.][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 가상 머신 확장

Service Fabric 가상 머신 확장을 사용하여 Azure Virtual Machines에 Service Fabric을 부트스트랩하고 노드 보안을 구성합니다.

다음은 Service Fabric 가상 머신 확장의 코드 조각입니다.

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

속성 설명은 다음과 같습니다.

| **이름** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
| name | 문자열 | 고유한 파일 이름 확장명 |
| type | "ServiceFabricLinuxNode" 또는 "ServiceFabricWindowsNode" | Service Fabric이 부트스트랩하는 OS를 확인합니다. |
| autoUpgradeMinorVersion | true 또는 false | SF 런타임 부 버전 자동 업그레이드 사용 |
| 게시자 | Microsoft.Azure.ServiceFabric | Service Fabric 확장 게시자의 이름 |
| clusterEndpont | 문자열 | URI: 관리 엔드포인트에 대한 포트 |
| nodeTypeRef | 문자열 | NodeType 이름 |
| durabilityLevel | 동, 은, 금, 백금 | 변경 불가능 Azure 인프라를 일시 중지할 수 있는 시간 |
| enableParallelJobs | true 또는 false | VM 제거와 같은 ParallelJobs 계산을 사용하도록 설정 및 동일한 확장 집합에서 VM 재부팅 |
| nicPrefixOverride | 문자열 | "10.0.0.0/24" 같은 서브넷 접두사 |
| commonNames | string[] | 설치된 클러스터 인증서의 일반 이름 |
| x509StoreName | 문자열 | 설치된 클러스터 인증서가 있는 저장소의 이름 |
| typeHandlerVersion | 1.1 | 확장 버전. 1.0 클래식 확장 버전을 1.1로 업그레이드하는 것이 좋습니다. |
| dataPath | 문자열 | Service Fabric 시스템 서비스 및 애플리케이션 데이터의 상태를 저장하는 데 사용되는 드라이브의 경로입니다.

## <a name="next-steps"></a>다음 단계

* ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)를 참조하세요.
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* 특정 확장 집합 인스턴스에 [원격으로 연결](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)
* 배포 후에 클러스터 VM에서 [RDP 포트 범위 값 업데이트](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 클러스터 VM에 대한 [관리자 사용자 이름 및 암호 변경](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
