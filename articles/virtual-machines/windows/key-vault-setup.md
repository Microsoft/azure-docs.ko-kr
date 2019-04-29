---
title: Azure Resource Manager에서 Windows VM에 대한 Key Vault 설정 | Microsoft Docs
description: Azure Resource Manager에서 사용할 주요 자격 증명 모음을 설정하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a8c29f015b6b3652361a886585cb4ccc3f3b7293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563454"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Azure Resource Manager에서 가상 머신에 대한 주요 자격 증명 모음 설정

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager 스택에서 비밀/인증서는 주요 자격 증명 모음 리소스 공급자가 제공하는 리소스로 모델링됩니다. Key Vault에 대한 자세한 내용을 보려면 [Azure Key Vault란?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. 주요 자격 증명 모음을 Azure Resource Manager 가상 머신에서 사용하려면 주요 자격 증명에 대한 **EnabledForDeployment** 속성을 true로 설정해야 합니다. 다양한 클라이언트에서 이 작업을 수행할 수 있습니다.
> 2. 주요 자격 증명 모음은 Virtual Machine과 동일한 구독 및 위치에 만들어야 합니다.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>PowerShell을 사용하여 주요 자격 증명 모음 설정
PowerShell을 사용하여 키 자격 증명 모음을 만들려면 [PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../../key-vault/quick-create-powershell.md)을 참조하세요.

새 주요 자격 증명의 경우 다음 PowerShell cmdlet을 사용할 수 있습니다.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

기존 주요 자격 증명의 경우 다음 PowerShell cmdlet을 사용할 수 있습니다.

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>CLI를 사용하여 주요 자격 증명 모음 설정
CLI(명령줄 인터페이스)를 사용하여 주요 자격 증명 모음을 만들려면 [CLI를 사용하여 키 자격 증명 모음 관리](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)를 참조하세요.

CLI의 경우 먼저 주요 자격 증명 모음을 만든 다음 배포 정책을 할당해야 합니다. 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

    az keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>템플릿을 사용하여 주요 자격 증명 모음 설정
템플릿을 사용하는 경우 `enabledForDeployment` 속성을 주요 자격 증명 모음 리소스에 대한 `true`로 설정해야 합니다.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

템플릿을 사용하여 주요 자격 증명 모음을 만들 때 구성할 수 있는 다른 옵션에 대해서는 [주요 자격 증명 모음 만들기](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)를 참조하세요.
