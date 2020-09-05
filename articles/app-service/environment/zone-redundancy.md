---
title: App Service 환경에 대 한 가용성 영역 지원
description: 앱이 영역 중복 되도록 App Service 환경을 배포 하는 방법에 대해 알아봅니다.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15b129db56a9c6854bc3c1f2814a8776ec39adc6
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961570"
---
# <a name="availability-zone-support-for-app-service-environments"></a>App Service 환경에 대 한 가용성 영역 지원

ASE (App Service 환경)를 가용성 영역 (AZ)에 배포할 수 있습니다.  고객은 ILB (내부 부하 분산 장치) Ase를 Azure 지역 내의 특정 AZ에 배포할 수 있습니다. ILB ASE를 특정 AZ에 고정 하는 경우 ILB ASE에서 사용 하는 리소스는 지정 된 AZ에 고정 되거나 영역 중복 방식으로 배포 됩니다.  

ILB ASE는 특정 영역에 고정 되므로 AZ에 명시적으로 배포 된 ILB ASE는 영역 리소스로 간주 됩니다. 다음 ILB ASE 종속성은 지정 된 영역에 고정 됩니다.

- ASE의 내부 부하 분산 장치 IP 주소
- ASE에서 웹 응용 프로그램을 관리 하 고 실행 하는 데 사용 하는 계산 리소스

영역 ILB ASE에 배포 된 웹 응용 프로그램의 원격 파일 저장소는 ZRS (영역 중복 저장소)를 사용 합니다.

이 문서에 설명 된 단계를 따르지 않는 한 ILB Ase는 자동으로 영역 방식으로 배포 되지 않습니다. 공용 IP 주소를 사용 하 여 외부 ASE를 특정 가용성 영역에 고정할 수 없습니다. 

영역 ILB Ase는 다음 지역 중 하나에서 만들 수 있습니다.

- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 동부 2 (EUAP)
- 프랑스 중부 
- 일본 동부
- 북유럽
- 서유럽
- 동남아시아
- 영국 남부
- 미국 서부 2

영역 ILB ASE에 배포 된 응용 프로그램은 계속 실행 되 고 해당 ASE에 대 한 트래픽을 제공 합니다. 같은 지역의 다른 영역이 중단 되는 경우에도 마찬가지입니다.  을 포함 하 여 런타임 동작이 아닐 수 있습니다. 응용 프로그램 서비스 계획 크기 조정, 응용 프로그램 생성, 응용 프로그램 구성 및 응용 프로그램 게시는 다른 가용성 영역에서 작동 중단의 영향을 받을 수 있습니다. 영역에 고정 된 영역 ILB ASE 배포는 이미 배포 된 응용 프로그램에 대 한 계속 가동 시간을 보장 합니다.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>가용성 영역에서 App Service Environment를 배포 하는 방법 ##

영역 ILB Ase는 ARM 템플릿을 사용 하 여 만들어야 합니다. 영역 ILB ASE를 ARM 템플릿을 통해 만든 후에는 Azure Portal 및 CLI를 통해 표시 하 고 상호 작용할 수 있습니다.  ARM 템플릿은 영역 ILB ASE를 처음 만들 때만 필요 합니다.

영역 ILB ASE를 지정 하기 위해 ARM 템플릿에서 필요한 유일한 변경 내용은 새 ***영역*** 속성입니다. ***Zones*** 속성은 ILB ASE가 고정 되어야 하는 논리 가용성 영역에 따라 "1", "2", "3"의 값으로 설정 되어야 합니다.

아래 예제 ARM 템플릿 코드 조각에서는 ILB ASE를 영역 2에 고정 하도록 지정 하는 새 ***영역*** 속성을 보여 줍니다.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

앱 영역을 중복 하 게 하려면 두 영역 ILB Ase를 배포 해야 합니다. 두 영역 ILB Ase은 별도의 가용성 영역에 있어야 합니다. 그런 다음 각 ILB Ase에 앱을 배포 해야 합니다. 앱을 만든 후에는 부하 분산 솔루션을 구성 해야 합니다. 권장 되는 해결책은 [영역 중복 Application Gateway 영역](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) Ilb ase의 업스트림을 배포 하는 것입니다. 

## <a name="in-region-data-residency"></a>지역 데이터 상주 ##

가용성 영역에 배포 된 ILB Ase는 지역 내에서 영역 ILB ASE가 배포 된 지역에만 고객 데이터를 저장 합니다. 웹 사이트 파일 콘텐츠와 고객 제공 설정 및 App Service에 저장 된 암호는 모두 영역 ILB ASE가 배포 되는 지역 내에 유지 됩니다.

고객은 "가용성 영역에서 App Service Environment를 배포 하는 방법" 섹션의 앞부분에 설명 된 단계를 수행 하 여 단일 지역 데이터를 상주 확인 합니다. 이러한 단계에 따라 App Service Environment를 구성 하 여 가용성 영역에 배포 된 App Service Environment [Azure 보안 센터](https://azuredatacentermap.azurewebsites.net/)에 지정 된 것을 포함 하 여 지역 데이터 상주 요구 사항을 충족 합니다.

고객은 다음 단계를 수행 하 여 단일 지역에 데이터를 저장 하도록 App Service Environment 올바르게 구성 되었는지 확인할 수 있습니다. 

1. [리소스 탐색기](https://resources.azure.com)를 사용 하 여 App Service Environment에 대 한 ARM 리소스로 이동 합니다.  Ase */hostingEnvironments*아래에 나열 됩니다.
2. *영역* 속성이 ARM json 구문 뷰에 있고 값이 "1", "2", "3" 인 단일 값 JSON 배열을 포함 하는 경우 ASE는 zonally 배포 되 고 고객 데이터는 동일한 지역에 유지 됩니다.
2. *Zones* 속성이 존재 하지 않거나 속성에 이전에 지정 된 유효한 영역 값이 없는 경우 ASE가 zonally 배포 되지 않고 고객 데이터가 동일한 지역에 독점적으로 저장 되지 않습니다.