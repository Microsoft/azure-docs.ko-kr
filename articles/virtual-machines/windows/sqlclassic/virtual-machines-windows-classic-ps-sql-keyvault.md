---
title: Azure의 Windows VM에서 Key Vault를 SQL Server에 통합(클래식) | Microsoft Docs
description: Azure Key Vault와 함께 사용하도록 SQL Server 암호화 구성을 자동화하는 방법에 대해 알아보세요. 이 항목에서는 Azure Key Vault 통합을 클래식 배포 모델에서 만든 SQL Server 가상 머신과 함께 사용하는 방법에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e20e2a094e1fd88dfc2a25b586dc6c894f92b418
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108480"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure Virtual Machines에서 SQL Server에 대한 Azure Key Vault 통합 구성(클래식)
> [!div class="op_single_selector"]
> * [리소스 관리자](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [클래식](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>개요
[TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx) [백업 암호화](https://msdn.microsoft.com/library/dn449489.aspx) 등 여러 SQL Server 암호화 기능이 있습니다. 이러한 형태의 암호화는 암호화에 사용되는 암호화 키를 관리 및 저장해야 합니다. AKV(Azure Key Vault) 서비스는 안전하고 가용성이 높은 위치에서 이러한 키의 보안 및 관리를 개선하도록 설계되었습니다. [SQL Server 커넥터](https://www.microsoft.com/download/details.aspx?id=45344)는 SQL Server가 Azure Key Vault의 키를 사용할 수 있게 해줍니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

온-프레미스 컴퓨터로 SQL Server를 실행하는 경우 [온-프레미스 SQL Server 컴퓨터에서 Azure Key Vault에 액세스할 수 있는 단계](https://msdn.microsoft.com/library/dn198405.aspx)가 있습니다. 하지만 Azure VM의 SQL Server에서는 *Azure Key Vault 통합* 기능을 사용하여 시간을 절약할 수 있습니다. 이 기능을 지원하는 Azure PowerShell cmdlet 몇 개만 있으면 SQL VM이 키 자격 증명 모음에 액세스하는 데 필요한 구성을 자동화할 수 있습니다.

이 기능은 활성화되면 자동으로 SQL Server 커넥터를 설치하고, Azure Key Vault에 액세스하도록 EKM 공급자를 구성하고, 사용자가 자격 증명 모음에 액세스할 수 있도록 자격 증명을 만듭니다. 앞에서 언급한 온-프레미스 설명서의 단계를 살펴보셨다면 이 기능이 2 및 3단계를 자동화한다는 것을 알 수 있습니다. 사용자가 수동으로 해야 하는 유일한 일은 키 자격 증명 모음 및 키를 만드는 작업입니다. 그 이후에 SQL VM을 설정하는 전체 과정이 자동화됩니다. 이 기능이 설정을 완료하면 사용자는 T-SQL 문을 실행하여 평소와 같이 데이터베이스 암호화 또는 백업을 시작할 수 있습니다.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV 통합 구성
PowerShell을 사용하여 Azure Key Vault 통합을 구성합니다. 다음 섹션에서는 필수 매개 변수 및 샘플 PowerShell 스크립트의 개요를 제공합니다.

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS 확장 설치
먼저 [SQL Server IaaS 확장을 설치](../classic/sql-server-agent-extension.md)합니다.

### <a name="understand-the-input-parameters"></a>입력 매개 변수 이해
다음 표에는 다음 섹션에는 PowerShell 스크립트를 실행하는 데 필요한 매개 변수가 나열되어 있습니다.

| 매개 변수 | 설명 | 예 |
| --- | --- | --- |
| **$akvURL** |**키 자격 증명 모음 URL** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**서비스 주체 이름** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**서비스 주체 암호** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**자격 증명 이름**: AKV 통합은 VM이 주요 자격 증명 모음에 액세스할 수 있도록 SQL Server 내에 자격 증명을 만듭니다. 이 자격 증명의 이름을 선택하세요. |"mycred1" |
| **$vmName** |**가상 머신 이름**: 이전에 만든 SQL VM의 이름입니다. |"myvmname" |
| **$serviceName** |**서비스 이름**: SQL VM과 연결된 클라우드 서비스 이름입니다. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>PowerShell과 AKV 통합 설정
**New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet은 Azure Key Vault Integration 기능에 대한 구성 개체를 만듭니다. **Set-AzureVMSqlServerExtension**은 **KeyVaultCredentialSettings** 매개 변수와의 통합을 구성합니다. 다음 단계에서는 이러한 명령을 사용하는 방법을 보여줍니다.

1. 이 항목의 이전 섹션에서 설명한 대로, 먼저 Azure PowerShell에서 특정 값을 사용하여 입력 매개 변수를 구성합니다. 다음은 스크립트 예입니다.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. 그런 후 다음 스크립트를 사용하여 AKV 통합을 구성 및 설정합니다.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 에이전트 확장에서 이 새로운 구성을 사용하여 SQL VM을 업데이트할 것입니다.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

