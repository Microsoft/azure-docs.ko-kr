---
title: Cloud 파트너 포털의 Azure Marketplace에 대한 가상 머신 SKU 탭 | Microsoft Docs
description: Azure Marketplace에서 가상 머신 제안을 만드는 데 사용되는 SKU 탭에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: e05813297707165d59d1b1ed969b54fb0f433277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844494"
---
# <a name="virtual-machine-skus-tab"></a>가상 머신 SKU 탭

**새 제안** 페이지의 **SKU** 탭을 사용하면 하나 이상의 SKU를 만들어 새 제안에 연결할 수 있습니다.  다양한 SKU에서 기능 집합, VM 이미지 유형, 처리량 또는 확장성, 청구 모델 또는 일부 다른 특성에 따라 솔루션을 구별할 수 있습니다.

## <a name="create-a-sku"></a>SKU 만들기

처음에는 새 제안에 연결된 SKU가 없으므로 **새 SKU**를 클릭하여 새 SKU를 만듭니다.

![가상 머신에 대한 새 제안 탭의 새 SKU 단추](./media/publishvm_005.png)

<br/>

**새 SKU** 대화 상자가 표시됩니다.  새 SKU에 대한 식별자를 입력한 다음, **확인**을 클릭합니다. (식별자 명명 규칙은 아래를 참조하세요.)  이제 **SKU** 탭에 편집하는 데 사용할 수 있는 필드가 표시됩니다.    필드 이름에 별표(*)가 추가되면 필수 항목임을 나타냅니다.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![가상 머신에 대한 새 제안 양식의 SKU 탭](./media/publishvm_006.png)

다음 표에서는 이러한 필드의 용도, 내용 및 형식을 설명합니다.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU 설정*   |  |
| **SKU ID**       | 이 SKU에 대한 식별자입니다.  이 이름은 소문자 영숫자 또는 대시(-)로 구성되며, 최대 50자이지만 대시로 끝날 수 없습니다.  제안이 게시되면 수정할 수 없습니다.  |
|  *SKU 세부 정보*   |  |
| **제목**        | 마켓플레이스에 표시할 제안에 대한 이름입니다. 최대 길이는 50자입니다. |
| **요약**      | 마켓플레이스에 표시할 제안에 대한 간결한 설명입니다. 최대 길이는 100자입니다. |
| **설명**  | 제안에 대한 자세한 설명을 제공하는 설명 텍스트입니다.  <!-- TD: max len/guidance? 3k characters -->  |
| **이 SKU 숨기기** | SKU가 마켓플레이스에서 고객에게 표시되어야 하는지 여부를 나타냅니다.  개별적으로 구입하지 않고 솔루션 템플릿을 통해서만 사용할 수 있도록 하려면 SKU를 숨기는 것이 좋습니다.  또한 초기 테스트 또는 임시/계절별 제안에도 유용할 수 있습니다. |
| **클라우드 가용성** | SKU를 사용할 수 있는 클라우드를 결정합니다.  기본값은 Azure의 공개 버전입니다.  Microsoft Azure Government는 미국 연방, 주, 지방 또는 부족 정부 및 해당 인증 파트너에 대한 액세스를 제어하는 정부-커뮤니티 클라우드입니다.  정부 클라우드에 대한 자세한 내용은 [Azure Government 시작](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)을 참조하세요. |
| **비공개 SKU인가요?** | SKU가 비공개 또는 공개인지 여부를 나타냅니다. 기본값은 **아니요**(공개)입니다.  자세한 내용은 [공개 및 비공개 SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)를 참조하세요. |
| **국가/지역 가용성** | SKU를 구입할 수 있는 국가 또는 세계 지역을 결정합니다. 하나 이상의 지역/국가를 선택합니다. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *가격*   |  |
| **라이선스 모델**| 사용할 표준화된 청구 모델입니다.  **사용량 기반 월별 청구 SKU**를 선택하면 코어당 가격 책정의 세부 정보와 체험 평가판 기간을 제공할지 여부를 지정할 수 있는 접이식 섹션이 열립니다.  이 섹션에서는 이 가격 책정 일정을 Excel로 내보내고 가져올 수도 있습니다. 자세한 내용은 [Azure Marketplace의 청구 옵션](../../billing-options-azure-marketplace.md)을 참조하세요. | 
|  *VM 이미지*   |  |
| **운영 체제 제품군** | 솔루션 VM이 Windows 또는 Linux 기반인지 여부를 나타냅니다. |
| **운영 체제 유형 선택** | 지정된 OS의 특정 공급업체 또는 릴리스입니다. |
| **OS 이름** | 고객에게 표시할 운영 체제 이름입니다.  |
| **권장되는 VM 크기** | 표준화된 목록에서 최대 6개의 권장되는 VM 크기를 선택할 수 있습니다.  이러한 권장 사항은 잠재 고객에게 눈에 잘 띄게 표시되지만, 솔루션 이미지와 호환되는 모든 VM 크기를 지정할 수 있습니다. | 
| **개방 포트**| 개방할 포트와 SKU를 지원할 프로토콜입니다.  이러한 구성은 솔루션 VM의 네트워크에 대해 구성된 가상 네트워크와 일치해야 합니다. 이러한 설정은 VM을 배포하는 중에 적용됩니다. 그러나 SKU가 게시되면 포트 설정을 수정할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 가상 머신에 대한 포털을 여는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)을 참조하세요. <br/>모든 VM에 추가되는 기본 네트워크 매핑은 다음과 같습니다. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22에 SSH TCP 22-&GT;합니다. |
| **디스크 버전**  | 디스크 버전 번호와 디스크 URL로 지정되는 연결된 솔루션 VM입니다. 디스크 버전은 [의미 체계 버전](https://semver.org/) 형식(`<major>.<minor>.<patch>`)이어야 합니다.  URL은 운영 체제 VHD에 대해 만든 공유 액세스 서명 URI입니다.  SKU당 최대 8개의 디스크 버전을 추가할 수 있지만, SKU에 대한 가장 높은 디스크 버전 번호만 Azure Marketplace에 표시됩니다. 다른 버전은 API를 통해서만 볼 수 있습니다.  <!--TD: Add more specific link to API --> <br/> **새 데이터 디스크** 접이식 섹션을 사용하면 최대 15개의 데이터 디스크를 VM에 연결할 수 있습니다.  지정된 VM 버전 및 연결된 데이터 디스크가 있는 SKU가 게시되면 이 구성을 수정할 수 없습니다.  추가 VM 버전이 SKU에 추가되면 동일한 수의 데이터 디스크도 지원해야 합니다. <br/> Azure 기반 VM 이미지를 만들지 않은 경우 나중에 이 필드 업데이트를 추가할 수 있습니다.  연결된 VM 리소스를 만드는 방법에 대한 자세한 내용은 [VM 기술 자산 만들기](./cpp-create-technical-assets.md) 섹션을 참조하세요.  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> **저장**을 선택하여 진행 상황을 저장합니다. 다음 탭에서 제안이 [시험 사용](./cpp-test-drive-tab.md)을 지원하는지 여부를 지정합니다.


## <a name="additional-pricing-considerations"></a>추가 가격 책정 고려 사항

위에 설명된 가격 책정 모델은 기본적인 설명입니다.  현재 변경을 진행하고 있으며 지방 또는 지역 세금 규정 및 Microsoft 가격 정책의 영향을 받을 수 있습니다. 

### <a name="simplified-currency-pricing"></a>간소화된 통화 가격 책정

2018년 9월 1일부터 **간소화된 통화 가격 책정**이라는 새 섹션이 포털에 추가됩니다. Microsoft는 전 세계 고객으로부터의 더욱 예측 가능한 가격 책정 및 요금 징수 방식을 사용하여 Azure Marketplace 비즈니스를 간소화하고 있습니다. 이 간소화에는 고객에게 발급되는 송장의 통화 단위 수를 줄이는 작업이 포함됩니다.  자세한 내용은 [Azure Marketplace에서 기존 VM 제안 업데이트](./cpp-update-existing-offer.md)를 참조하세요.


### <a name="additional-information-on-taxes-and-prices"></a>세금 및 가격에 대한 추가 정보

* Microsoft는 일부 국가를 *세금 송금 국가* 로 분류합니다.  이러한 국가에서 Microsoft는 고객으로부터 세금을 징수한 다음, 정부에 세금을 납부(송금)합니다.  다른 국가에서는 일반적으로 파트너가 고객으로부터 세금을 징수하고 정부에 세금을 납부해야 합니다. 후자의 국가에서 판매하기로 선택한 경우 지방세를 계산하고 지불할 수 있는 능력이 있어야 합니다.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* 제품이 라이브 상태가 된 후에는 가격을 변경할 수 없습니다. 그러나 지원되는 지역은 여전히 추가하거나 제거할 수 있습니다. 
* Microsoft는 예약된 SKU 요금 외에도 표준 Azure VM 사용 요금을 고객에게 부과합니다.
* 가격은 모든 하위 지역에 대해 가격을 설정할 당시에 사용 가능한 환율의 현지 통화로 설정됩니다.  <!-- TD: Meaning? - Offer created, published, other? -->
* 각 지역의 가격을 개별적으로 설정하려면 가격 스프레드시트를 내보내고, 사용자 지정 가격 책정을 적용한 다음, 가져옵니다. 

<!-- TD: The detailed information in the table and supplemental notes should be centralized in another topic, maybe "Billing Options" in AMP tree. Need to include a common section on export/import pricing-->

