---
title: Azure Virtual Machine 사용 이해 | Microsoft Docs
description: 가상 머신 사용 세부 정보 이해
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: ''
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: b515a0b226723989b1cc73356f1377da421dc9aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485656"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Azure Virtual Machine 사용 이해 
Azure 사용 데이터를 분석하면 조직 전체의 비용 관리와 할당을 개선할 수 있는 강력한 소비 통찰력을 확보할 수 있습니다. 이 문서에서는 Azure Compute 소비에 대한 심층적인 세부 정보를 제공합니다. 일반 Azure 사용에 대한 자세한 내용은 [청구서 이해](../../billing/billing-understand-your-bill.md)로 이동합니다.

## <a name="download-your-usage-details"></a>사용 세부 정보 다운로드
먼저 [사용 세부 정보를 다운로드](../../billing/billing-download-azure-invoice-daily-usage-date.md)합니다. 다음 표에서는 Azure Resource Manager를 통해 배포된 Virtual Machines의 정의 및 예제 값을 제공합니다. 이 문서에는 클래식 모델을 통해 배포된 VM에 대한 상세 정보는 없습니다.


| 필드             | 의미                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 예제 값                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 사용 날짜         | 리소스를 사용한 날짜입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| 측정기 ID           | 이 사용이 속한 최상위 서비스를 식별합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | “Virtual Machines”                                                                                                                                                                                                                                                                                                                                               |
| 측정기 하위 범주 | 청구된 측정기 식별자입니다. <ul><li>컴퓨팅 시간 사용에 대해 각 VM 크기 + OS(Windows, 비 Windows) + 지역에 대한 미터가 있습니다.</li><li>프리미엄 소프트웨어 사용의 경우 각 소프트웨어 유형에 대해 미터가 있습니다. 대부분의 프리미엄 소프트웨어 이미지는 각 코어 크기마다 서로 다른 미터를 갖습니다. 자세한 내용은 [Compute 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/)를 참조하세요.</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| 미터 이름         | Azure의 각 서비스마다 특정합니다. Compute의 경우 항상 “컴퓨팅 시간”입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | “컴퓨팅 시간”                                                                                                                                                                                                                                                                                                                                                  |
| 미터 영역       | 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  “JA East”                                                                                                                                                                                                                                                                                                                                                       |
| 단위               | 서비스 요금이 청구되는 단위를 식별합니다. 계산 리소스는 시간당 요금이 청구됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | “Hours”                                                                                                                                                                                                                                                                                                                                                          |
| 사용           | 해당 날짜에 사용된 리소스의 양입니다. Compute의 경우 일정 시간동안 실행된 VM에 대해 분마다 비용을 청구합니다(최대 소수점 6자리의 정확도).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| 리소스 위치  | 리소스가 실행되고 있는 데이터 센터를 식별합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | “JA East”                                                                                                                                                                                                                                                                                                                                                        |
| 사용되는 서비스   | 사용한 Azure 플랫폼 서비스입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| 리소스 그룹     |  배포된 리소스가 실행되는 리소스 그룹입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| 인스턴스 ID        | 리소스에 대한 식별자입니다. 식별자를 만들 때 리소스에 대해 지정한 이름을 포함합니다. VM의 경우 인스턴스 ID는 SubscriptionId, ResourceGroupName 및 VMName(또는 확장 집합 사용에 대한 확장 집합 이름)을 포함합니다.                                                                                                                                                                                                                                                                                                                                                                                                                    | "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1”<br><br>또는<br><br>"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1”                                                                                           |
| 태그들               | 리소스에 할당하는 태그입니다. 태그를 사용하여 청구 레코드를 그룹화합니다. [Virtual Machines에 태그를 지정하는 방법](tag.md)을 알아봅니다. Resource Manager VM에만 사용할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| 추가 정보    | 서비스 특정 메타데이터입니다. VM에 대해 추가 정보 필드에 다음을 채웁니다. <ul><li>이미지 형식 - 실행한 특정 이미지. 이미지 형식 아래에서 지원되는 문자열의 전체 목록을 찾습니다.</li><li>서비스 형식: 배포한 크기입니다.</li><li>VMName: VM 이름입니다. 확장 집합 VM에 대해서만 채워집니다. 확장 집합 VM에 VM 이름이 필요한 경우 위의 인스턴스 ID 문자열에서 확인할 수 있습니다.</li><li>UsageType: 이 나타내는 사용 유형을 지정 합니다.<ul><li>ComputeHR은 Standard_D1_v2 같은 기본 VM의 컴퓨팅 시간 사용입니다.</li><li>ComputeHR_SW는 Microsoft R Server처럼 VM이 프리미엄 소프트웨어를 사용할 경우 프리미엄 소프트웨어 요금입니다.</li></ul></li></ul>    | Virtual Machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtual Machine Scale Sets {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>프리미엄 소프트웨어 {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>이미지 형식
Azure 갤러리의 일부 이미지의 경우 추가 정보 필드에 이미지 형식이 채워집니다. 이를 통해 사용자는 자신의 가상 머신에 배포된 항목을 이해하고 추적할 수 있습니다. 배포한 이미지에 따라 이 필드에 채워지는 값은 다음과 같습니다.
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Open Logic 
  - Oracle 
  - SAP용 SLES 
  - Windows Server 2012 R2 미리 보기에서 SQL Server 14 미리 보기 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - SAP Business Applications용 Red Hat     
  - SAP HANA용 Red Hat 
  - Windows 클라이언트 BYOL 
  - Windows 서버 BYOL 
  - Windows Server 미리 보기 

## <a name="service-type"></a>서비스 유형
추가 정보 필드에 있는 서비스 유형 필드는 배포한 정확한 VM 크기에 해당합니다. Premium Storage VM(SSD 기반) 및 비 Premium Storage VM(HDD 기반)은 같은 가격이 책정됩니다. Standard\_DS2\_v2 같은 SSD 기반 크기를 배포한 경우, 미터 하위 카테고리 열에는 비 SSD 크기(‘Standard\_D2\_v2 VM’), 추가 정보 필드에는 SSD 크기(‘Standard\_DS2\_v2’)가 표시됩니다.

## <a name="region-names"></a>지역 이름
사용 세부 정보의 리소스 위치 필드에 채워진 지역 이름은 Azure Resource Manager에서 사용된 지역 이름마다 다릅니다. 지역 값 사이의 매핑은 다음과 같습니다.

|    **Resource Manager 지역 이름**       |    **사용 세부 정보의 리소스 위치**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    오스트레일리아 동부                               |
|    australiasoutheast    |    오스트레일리아 남동부                          |
|    brazilsouth           |    브라질 남부                              |
|    CanadaCentral         |    캐나다 중부                            |
|    CanadaEast            |    캐나다 동부                               |
|    CentralIndia          |    인도 중부                            |
|    centralus             |    미국 중부                            |
|    chinaeast             |    중국 동부                            |
|    chinanorth            |    중국 북부                           |
|    eastasia              |    동아시아                             |
|    eastus                |    미국 동부                               |
|    eastus2               |    미국 동부 2                             |
|    GermanyCentral        |    독일 중부                            |
|    GermanyNortheast      |    독일 북동부                          |
|    japaneast             |    일본 동부                               |
|    japanwest             |    일본 서부                               |
|    KoreaCentral          |    한국 중부                            |
|    KoreaSouth            |    한국 남부                              |
|    northcentralus        |    미국 중북부                      |
|    northeurope           |    북유럽                          |
|    southcentralus        |    미국 중남부                      |
|    southeastasia         |    동남아시아                        |
|    SouthIndia            |    인도 남부                              |
|    UKNorth               |    영국 북부                              |
|    uksouth               |    영국 남부                              |
|    UKSouth2              |    영국 남부 2                            |
|    ukwest                |    영국 서부                               |
|    USDoDCentral          |    미국 국방부 중부                        |
|    USDoDEast             |    미국 국방부 동부                           |
|    USGovArizona          |    USGov 애리조나                         |
|    usgoviowa             |    미국 정부 아이오와                            |
|    USGovTexas            |    USGov 텍사스                           |
|    usgovvirginia         |    USGov 버지니아                        |
|    westcentralus         |    미국 중서부                       |
|    westeurope            |    서유럽                           |
|    WestIndia             |    인도 서부                               |
|    westus                |    미국 서부                               |
|    westus2               |    미국 서부 2                             |


## <a name="virtual-machine-usage-faq"></a>가상 머신 사용 FAQ 
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>VM을 배포할 때 어떤 리소스에 요금이 청구되나요?    
VM은 VM 자체, VM에서 실행되는 프리미엄 소프트웨어, VM에 연결된 저장소 계정\관리되는 디스크, VM으로부터의 네트워킹 대역폭 전송에 대해 비용이 발생합니다.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>VM이 Azure Hybrid Benefit을 사용하는 경우 사용 CSV에서 어떻게 확인하나요?
[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 배포하는 경우 자체 라이선스를 클라우드로 가져오는 것이므로 비 Windows VM 요금이 부과됩니다. 청구서에서 ImageType 열에 “Windows\_Server BYOL” 또는 “Windows\_Client BYOL”이 표시되므로 어떤 Resource Manager VM이 Azure Hybrid Benefit을 실행하는지 구분할 수 있습니다. 
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>사용 CSV에서 기본과 사용 CSV에서 표준 VM 유형은 어떻게 다른가요?
기본 및 표준 A 시리즈 VM이 모두 제공됩니다. 기본 VM을 배포한 경우 미터 하위 카테고리에 “Basic”이라는 문자열이 있습니다. 표준 A 시리즈 VM을 배포한 경우 표준이 기본값이므로 VM 크기가 “A1 VM”으로 표시됩니다. 기본과 표준의 차이점에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/)를 참조하세요.
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>ExtraSmall, Small, Medium, Large 및 ExtraLarge 크기는 무엇인가요?
ExtraSmall - ExtraLarge는 표준 \_A0 – Standard\_A4의 레거시 이름입니다. 클래식 VM 사용 레코드에서는 해당 크기를 배포한 경우 이 규칙이 사용되는 것을 확인할 수 있습니다.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>미터 지역과 리소스 위치 사이의 차이점은 무엇인가요?
미터 지역은 미터와 연결됩니다. 모든 지역에 동일 가격을 사용하는 일부 Azure 서비스에서 미터 지역 필드는 공백입니다. 그러나 VM에는 Virtual Machines에 대해 지역 전담 가격이 있으므로 이 필드가 채워집니다. 마찬가지로, Virtual Machines에 대한 리소스 지역은 VM이 배포된 위치입니다. 지역 이름에 다른 문자열 규칙이 있을 수 있으나 두 필드의 Azure 지역은 동일합니다.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>추가 정보 필드에서 ImageType 값이 비어 있는 이유는 무엇인가요?
ImageType 필드는 이미지의 하위 집합에 대해서만 채워집니다. 위의 이미지 중 하나를 배포하지 않은 경우 ImageType이 비어 있습니다.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>추가 정보에서 VMName이 비어 있는 이유는 무엇인가요?
VMName은 확장 집합에서 VM에 대한 추가 정보에만 채워집니다. InstanceID 필드에는 비 확장 집합 VM에 대한 VM 이름이 있습니다.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>추가 정보의 UsageType 필드에서 ComputeHR은 무엇을 의미하나요?
ComputeHR은 기본 인프라 비용에 대한 사용 이벤트를 나타내는 컴퓨팅 시간을 나타냅니다. UsageType이 ComputeHR\_SW이면 사용 이벤트가 VM에 대한 프리미엄 소프트웨어 요금을 의미합니다.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>프리미엄 소프트웨어의 요금을 어떻게 알 수 있나요?
어떤 VM 이미지가 자신의 요구에 가장 부합하는지 모색할 때는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute)를 확인합니다. 이미지에는 소프트웨어 계획 요금이 있습니다. 요금이 "Free"로 표시된 경우 해당 소프트웨어에는 추가 비용이 없는 것입니다. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>사용 서비스에서 Microsoft.ClassicCompute와 Microsoft.Compute의 차이는 무엇인가요?
Microsoft.ClassicCompute는 Azure Service Manager를 통해 배포된 클래식 리소스를 나타냅니다. Resource Manager를 통해 배포하는 경우 사용되는 서비스에서 Microsoft.Compute가 채워집니다. Azure [배포 모델](../../azure-resource-manager/resource-manager-deployment-model.md)에 대해 자세히 알아보세요.
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>내 Virtual Machine 사용에 대해 InstanceID 필드가 비어 있는 이유는 무엇인가요?
클래식 배포 모델을 통해 배포할 경우 InstanceID 문자열을 사용할 수 없습니다.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>내 VM에 대한 태그가 사용 세부 정보에 전달되지 않는 이유는 무엇인가요?
태그는 Resource Manager VM에 대해서만 사용 CSV로 전달됩니다. 클래식 리소스 태그는 사용 세부 정보에서 사용할 수 없습니다.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>어떻게 사용 크기가 하루 24시간보다 커질 수 있나요?
클래식 모델에서 리소스 요금 청구는 클라우드 서비스 수준에서 집계됩니다. 동일한 요금 청구 미터를 사용하는 클라우드 서비스에 여러 VM이 있는 경우 사용이 한꺼번에 집계됩니다. Resource Manager를 통해 배포된 VM은 VM 수준에서 청구되므로 이 집계가 적용되지 않습니다.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>가격 책정 페이지에서 DS/FS/GS/LS 크기에 대한 가격 책정을 왜 사용할 수 없나요?
Premium Storage를 지원하는 VM은 비 Premium Storage 지원 VM과 같은 요금으로 청구됩니다. 저장소 비용만 다릅니다. 자세한 내용은 [저장소 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
사용 세부 정보에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](../../billing/billing-understand-your-bill.md)를 참조하세요.

