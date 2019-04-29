---
title: Azure Service Fabric Mesh에서 실행 중인 앱 자동 크기 조정 | Microsoft Docs
description: Service Fabric Mesh 애플리케이션의 서비스에 대 한 자동 크기 조정 정책을 구성하는 방법에 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2233dffabd7c76ca55cf215f8bc04e66134f5799
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583607"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Service Fabric Mesh 애플리케이션에 대한 자동 크기 조정 정책 만들기
Service Fabric Mesh에 애플리케이션을 배포하여 얻을 수 있는 주요 이점 중 하나는 서비스를 쉽게 확장 또는 축소하는 기능입니다. 이 기능을 사용하여 서비스에서 다양한 크기의 부하를 처리하거나 가용성을 향상할 수 있습니다. 수동으로 서비스를 확대 또는 축소하거나 자동 크기 조정 정책을 설정할 수 있습니다.

[자동 크기 조정](service-fabric-mesh-scalability.md#autoscaling-service-instances)를 사용하여 서비스 인스턴스의 수를 동적으로 조정할 수 있습니다(수평 크기 조정). 자동 크기 조정은 탄력성이 뛰어나 CPU 또는 메모리 사용률에 따라 서비스 인스턴스를 프로비전하거나 제거할 수 있습니다.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>자동 크기 조정 정책, 트리거 및 메커니즘을 만들기 위한 옵션
자동 크기 조정 정책은 크기를 조정하려는 각 서비스에 대해 정의됩니다. 정책은 YAML 서비스 리소스 파일 또는 JSON 배포 템플릿으로 정의됩니다. 각 크기 조정 정책은 트리거 및 크기 조정 메커니즘 두 부분으로 구성됩니다.

트리거는 자동 크기 조정 정책이 호출될 시간을 정의합니다.  트리거(평균 부하) 및 메트릭의 종류를 모니터(CPU 또는 메모리)에 지정합니다.  상한 및 하한 임계값을 백분율로 지정합니다. 눈금 간격은 현재 배포된 모든 서비스 인스턴스에서 지정된 사용률(예: 평균 CPU 로드)을 얼마나 자주 확인하는지(초 단위)를 정의합니다.  모니터링된 메트릭이 하한 임계값 아래로 떨어지거나 상한 임계값 위로 증가할 때 메커니즘이 트리거됩니다.  

크기 조정 메커니즘은 정책이 트리거될 때 크기 조정 작업을 수행하는 방법을 정의합니다.  메커니즘의 종류(복제본 추가/제거)를 지정하면 최소 및 최대 복제본을 계산합니다(정수).  서비스 복제본의 수는 최소 수 이하 또는 최대 수 이상으로 크기 조정되지 않습니다.  또한 조정 작업에서 추가 또는 제거할 수 있는 복제본의 수인 크기 조정 증분을 정수로 지정합니다.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>JSON 템플릿에서 자동 크기 조정 정책 정의

다음 예제는 JSON 배포 템플릿에서 자동 크기 조정 정책을 보여 줍니다.  자동 크기 조정 정책은 크기를 조정할 서비스의 속성에서 선언됩니다.  이 예제에서는 CPU 평균 로드 트리거가 정의됩니다.  모든 배포 인스턴스의 평균 CPU 로드가 0.2(20%) 미만으로 떨어지거나 (80%)0.8 이상으로 올라가는 경우 메커니즘은 트리거됩니다.  CPU 로드는 60초마다 확인됩니다.  정책이 트리거될 경우 크기 조정 메커니즘은 인스턴스를 추가 또는 제거하도록 정의됩니다.  서비스 인스턴스는 1 단위로 추가되거나 제거됩니다.  또한 최소 인스턴스 수는 1로 최대 인스턴스 수는 40으로 정의됩니다.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Service.yaml 리소스 파일에서 자동 크기 조정 정책 정의
다음 예제는 서비스 리소스(YAML) 파일에서 자동 크기 조정 정책을 보여줍니다.  자동 크기 조정 정책은 크기를 조정할 서비스의 속성에서 선언됩니다.  이 예제에서는 CPU 평균 로드 트리거가 정의됩니다.  모든 배포 인스턴스의 평균 CPU 로드가 0.2(20%) 미만으로 떨어지거나 (80%)0.8 이상으로 올라가는 경우 메커니즘은 트리거됩니다.  CPU 로드는 60초마다 확인됩니다.  정책이 트리거될 경우 크기 조정 메커니즘은 인스턴스를 추가 또는 제거하도록 정의됩니다.  서비스 인스턴스는 1 단위로 추가되거나 제거됩니다.  또한 최소 인스턴스 수는 1로 최대 인스턴스 수는 40으로 정의됩니다.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>다음 단계
[수동으로 서비스를 크기 조정](service-fabric-mesh-tutorial-template-scale-services.md)하는 방법 알아보기
