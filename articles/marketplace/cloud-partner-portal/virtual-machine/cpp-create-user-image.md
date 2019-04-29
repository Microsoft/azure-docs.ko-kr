---
title: Azure Marketplace를 위한 사용자 VM 이미지 만들기 | Microsoft Docs
description: 사용자 VM 이미지를 만드는 데 필요한 단계와 참조를 나열합니다.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744111"
---
# <a name="create-a-user-vm-image"></a>사용자 VM 이미지 만들기

이 문서에서는 일반화된 VHD에서 관리되지 않는 이미지를 만드는 데 필요한 두 가지 일반 단계를 설명합니다.  이미지 캡처 및 이미지 일반화의 각 단계를 안내하는 참조가 제공됩니다.


## <a name="capture-the-vm-image"></a>VM 이미지 캡처

액세스 방법에 해당하는 VM 캡처에 대한 다음 문서의 지침을 사용합니다.

-  PowerShell: [Azure VM에서 관리되지 않는 VM 이미지를 만드는 방법](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [가상 머신 또는 VHD의 이미지를 만드는 방법](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines - 캡처](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>VM 이미지 일반화

이전에 일반화된 VHD에서 사용자 이미지를 생성했으므로 이를 일반화해야 합니다.  다시 한 번 액세스 메커니즘에 해당하는 다음 문서를 선택합니다.  (디스크를 캡처할 때 이미 디스크를 일반화했을 수 있습니다.)

-  PowerShell: [VM 일반화](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [2단계: VM 이미지 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines - 일반화](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>다음 단계

다음으로, [인증서를 만들고](cpp-create-key-vault-cert.md) 새 Azure Key Vault에 저장합니다.  이 인증서는 VM에 대한 보안 WinRM 연결을 설정하는 데 필요합니다.
