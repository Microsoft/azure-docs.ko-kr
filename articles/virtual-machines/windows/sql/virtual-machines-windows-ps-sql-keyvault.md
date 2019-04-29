---
title: Azure의 Windows VM에서 Key Vault를 SQL Server에 통합(Resource Manager) | Microsoft Docs
description: Azure Key Vault와 함께 사용하도록 SQL Server 암호화 구성을 자동화하는 방법에 대해 알아보세요. 이 항목에서는 Azure Key Vault 통합을 Resource Manager로 만든 SQL Server 가상 머신과 함께 사용하는 방법에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ad8eea21c10726b2c3eaf1e10bfd5efba4d1e48
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129623"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines에서 SQL Server에 대한 Azure Key Vault 통합 구성(Resource Manager)

> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-ps-sql-keyvault.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>개요
[TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx) [백업 암호화](https://msdn.microsoft.com/library/dn449489.aspx) 등 여러 SQL Server 암호화 기능이 있습니다. 이러한 형태의 암호화는 암호화에 사용되는 암호화 키를 관리 및 저장해야 합니다. AKV(Azure Key Vault) 서비스는 안전하고 가용성이 높은 위치에서 이러한 키의 보안 및 관리를 개선하도록 설계되었습니다. [SQL Server 커넥터](https://www.microsoft.com/download/details.aspx?id=45344)는 SQL Server가 Azure Key Vault의 키를 사용할 수 있게 해줍니다.

온-프레미스 컴퓨터로 SQL Server를 실행하는 경우 [온-프레미스 SQL Server 컴퓨터에서 Azure Key Vault에 액세스할 수 있는 단계](https://msdn.microsoft.com/library/dn198405.aspx)가 있습니다. 하지만 Azure VM의 SQL Server에서는 *Azure Key Vault 통합* 기능을 사용하여 시간을 절약할 수 있습니다.

이 기능은 활성화되면 자동으로 SQL Server 커넥터를 설치하고, Azure Key Vault에 액세스하도록 EKM 공급자를 구성하고, 사용자가 자격 증명 모음에 액세스할 수 있도록 자격 증명을 만듭니다. 앞에서 언급한 온-프레미스 설명서의 단계를 살펴보셨다면 이 기능이 2 및 3단계를 자동화한다는 것을 알 수 있습니다. 사용자가 수동으로 해야 하는 유일한 일은 키 자격 증명 모음 및 키를 만드는 작업입니다. 그 이후에 SQL VM을 설정하는 전체 과정이 자동화됩니다. 이 기능이 설정을 완료하면 사용자는 T-SQL 문을 실행하여 평소와 같이 데이터베이스 암호화 또는 백업을 시작할 수 있습니다.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM 공급자 버전 1.0.4.0은 [SQL IaaS 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)을 통해 SQL Server VM에 설치됩니다. SQL IaaS 확장을 업그레이드해도 공급자 버전이 업데이트되지는 않습니다. SQL Managed Instance로 마이그레이션하는 등 필요한 경우에는 EKM 공급자 버전을 수동으로 업그레이드하세요.


## <a name="enabling-and-configuring-akv-integration"></a>AKV 통합 설정 및 구성
기존 VM에 대해 프로비전닝 또는 구성하는 동안 AKV 통합을 설정할 수 있습니다.

### <a name="new-vms"></a>새 VM
Resource Manager와 함께 새 SQL Server 가상 머신을 프로비저닝하는 경우 Azure Portal은 Azure Key Vault 통합을 구현하는 방법을 제공합니다. Azure Key Vault 기능은 SQL Server Enterprise, Developer 및 평가판 버전에만 사용할 수 있습니다.

![SQL Azure Key Vault 통합](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

프로비전의 자세한 연습은 [Azure Portal에서 SQL Server 가상 머신 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

### <a name="existing-vms"></a>기존 VM
기존 SQL Server 가상 머신에 대한 해당 SQL Server 가상 머신을 선택합니다. 그런 다음 **설정** 블레이드의 **SQL Server 구성** 섹션을 선택합니다.

![기존 VM에 대한 SQL AKV 통합](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

**SQL Server 구성** 블레이드에서 자동화된 Key Vault 통합 섹션의 **편집** 단추를 클릭합니다.

![기존 VM에 대한 SQL AKV 통합 구성](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

완료되면 **SQL Server 구성** 블레이드 아래쪽의 **확인** 단추를 클릭하여 변경 내용을 저장합니다.

> [!NOTE]
> 여기서 만든 자격 증명 이름은 나중에 SQL 로그인에 매핑됩니다. 이렇게 하면 SQL 로그인이 키 자격 증명 모음에 액세스할 수 있습다. 
>
>

> [!NOTE]
> 또한 템플릿을 사용하여 AKV 통합을 구성할 수 있습니다. 자세한 내용은 [Azure Key Vault 통합을 위한 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)을 참조하세요.
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

