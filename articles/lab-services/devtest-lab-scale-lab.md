---
title: Azure DevTest Labs에서 랩의 할당량 및 한도 규모 조정 | Microsoft Docs
description: Azure DevTest Labs에서 랩 규모를 조정하는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ec79f6a9b255d44e66b901a0aae263c8dbbf2863
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623510"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>DevTest Labs의 할당량 및 한도 규모 조정
DevTest Labs에서 작업할 때 일부 Azure 리소스에 대한 특정 기본 한도가 있으며 이 한도가 DevTest Labs 서비스에 영향을 미칠 수 있습니다. 이러한 한도를 **할당량**이라고 합니다.

> [!NOTE]
> DevTest Labs 서비스는 할당량을 적용하지 않습니다. 발생할 수 있는 모든 할당량은 전체 Azure 구독의 기본 제약 조건입니다.

할당량에 도달할 때까지 각 Azure 리소스를 사용할 수 있습니다. 각 구독에는 별도의 할당량이 있으며 구독마다 사용량이 추적됩니다.

예를 들어 각 구독의 기본 할당량은 20 코어입니다. 따라서 랩에서 코어가 4개인 VM을 만드는 경우 총 5개의 VM을 만들 수 있습니다.

[Azure 구독 및 서비스 한도](https://docs.microsoft.com/azure/azure-subscription-service-limits)에는 Azure 리소스에 대 한 가장 일반적인 할당량 중 일부가 나열되어 있습니다. 랩에서 가장 일반적으로 사용되며 사용자가 경험할 가능성이 있는 리소스에는 VM 코어, 공용 IP 주소, 네트워크 인터페이스, 관리되는 디스크, RBAC 역할 할당 및 ExpressRoute 회로가 포함됩니다.

## <a name="view-your-usage-and-quotas"></a>사용량 및 할당량 보기
다음 단계에서는 특정 Azure 리소스에 대한 구독의 현재 할당량 그리고 사용한 각 할당량의 비율을 보는 방법을 보여 줍니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **추가 서비스**를 선택한 후 목록에서 **청구**를 선택합니다.
1. 청구 블레이드에서 구독을 선택합니다.
4. **사용량 + 할당량**을 선택합니다.

   ![사용량 및 할당량 단추](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   해당 구독에서 사용 가능한 여러 리소스 및 리소스당 사용되고 있는 할당량 비율을 나열하는 사용량 + 할당량 블레이드가 나타납니다.

   ![할당량 및 사용량](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>구독에 더 많은 리소스 요청
할당량 제한에 도달하는 경우 [Azure 구독 및 서비스 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits)의 설명에 따라 구독의 기본 리소스 한도를 최대 한도까지 늘릴 수 있습니다.

다음 단계에서는 [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)을 통해 할당량 증가를 요청하는 방법을 보여 줍니다.

1. **추가 서비스**, **청구**, **사용량 + 할당량**을 차례로 선택합니다.
1. 사용량 + 할당량 블레이드에서 **증가 요청** 단추를 선택합니다.

   ![증가 요청 단추](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. 요청을 완료하고 제출하려면 **새 지원 요청** 양식의 세 탭에 필수 정보를 모두 입력합니다.

   ![증가 요청 양식](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

Azure 지원에 할당량 증가를 요청하는 자세한 방법은 [Azure 한도 및 증가의 이해](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)를 참조하세요.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>다음 단계
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)를 탐색합니다.
