---
title: Azure 구독 상태
description: 이 문서에서는 Azure 구독의 다양한 상태를 설명합니다.
keywords: Azure 구독 상태
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 70d03b4aeeaecee522552b381ce1309485719dc6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686686"
---
# <a name="azure-subscription-states"></a>Azure 구독 상태

이 문서에서는 Azure 구독에 포함될 수 있는 다양한 상태를 설명합니다. 이러한 상태는 Azure Portal 구독 영역에서 **상태**로 표시됩니다.

| 구독 상태 | Description |
|-------------| ----------------|
| **활성**/**사용** | Azure 구독이 활성 상태입니다. 구독을 사용하여 새 리소스를 배포하고 기존 리소스를 관리할 수 있습니다.<br><br>[구독에 등록](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)된 리소스 공급자는 모든 작업(PUT, PATCH, DELETE, POST, GET)을 사용할 수 있습니다. |
| **삭제됨** | Azure 구독이 모든 기본 리소스/데이터와 함께 삭제되었습니다.<br><br>사용 가능한 작업이 없습니다. |
| **사용 안 함** | Azure 구독이 비활성화되어 더 이상 Azure 리소스를 만들거나 관리하는 데 사용할 수 없습니다. 이 상태에서는 가상 머신의 할당이 취소되고 임시 IP 주소가 해제되며 스토리지는 읽기 전용이 되고 다른 서비스는 비활성 상태가 됩니다. 다음과 같은 이유로 구독을 사용하지 않도록 설정할 수 있습니다. 크레딧이 만료되었을 수 있습니다. 지출 한도에 도달했을 수 있습니다. 기한이 초과된 청구서가 있습니다. 신용 카드 한도가 초과되었습니다. 또는 명시적으로 사용하지 않도록 설정되거나 취소되었습니다. 구독 유형에 따라 구독은 1-90일 사이에서 사용하지 않도록 설정된 상태로 유지될 수 있습니다. 그 후에는 영구적으로 삭제됩니다. 자세한 내용은 [비활성화된 Azure 구독 다시 활성화](subscription-disabled.md)를 참조하세요.<br><br>리소스 만들기 또는 업데이트 작업(PUT, PATCH)이 비활성화되어 있습니다. 동작을 수행하는 작업(POST)도 비활성됩니다. 리소스를 검색하거나 삭제할 수 있습니다(GET, DELETE). 리소스를 계속 사용할 수 있습니다. |
| **만료됨** | Azure 구독이 취소되어 만료되었습니다. 만료된 구독을 다시 활성화할 수 있습니다. 자세한 내용은 [비활성화된 Azure 구독 다시 활성화](subscription-disabled.md)를 참조하세요.<br><br>리소스 만들기 또는 업데이트 작업(PUT, PATCH)이 비활성화되어 있습니다. 동작을 수행하는 작업(POST)도 비활성됩니다. 리소스를 검색하거나 삭제할 수 있습니다(GET, DELETE).|
| **기한 초과** | Azure 구독에 보류 중인 미해결 결제가 있습니다. 구독은 아직 활성 상태이지만 비용을 지불하지 못하면 구독이 비활성화될 수 있습니다. 자세한 내용은 [Azure 구독의 미지불액 해결](resolve-past-due-balance.md)을 참조하세요.<br><br>모든 작업이 가능합니다. |
| **경고함** | Azure 구독은 경고 상태에 있으며 정상적으로 사용할 수 있지만 경고 이유가 해결되지 않으면 곧 비활성화됩니다. 구독 기간이 지났거나, 사용자가 취소했거나, 구독이 만료된 경우 구독이 경고 상태일 수 있습니다.<br><br>모든 작업이 가능합니다. |
