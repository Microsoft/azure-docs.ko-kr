---
title: SQL Server Virtual Machines의 자동화된 백업(클래식) | Microsoft Docs
description: '리소스 관리자를 사용하여 Azure Virtual Machines에서 실행 중인 SQL Server에 대한 자동화된 Backup 기능에 대해 설명합니다. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607709"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Azure Virtual Machines에서 SQL Server의 자동화된 백업(클래식)
> [!div class="op_single_selector"]
> * [리소스 관리자](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [클래식](../classic/sql-automated-backup.md)
> 
> 

자동화된 Backup에서는 SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM의 모든 기존 및 새 데이터베이스에 대해 [Microsoft Azure에 대한 관리되는 Backup](https://msdn.microsoft.com/library/dn449496.aspx) 을 자동으로 구성합니다. 이를 통해 지속형 Azure Blob Storage를 활용하는 일반 데이터베이스 백업을 구성할 수 있습니다. 자동화된 Backup은 [SQL Server IaaS 에이전트 확장](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)에 따라 다릅니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. 이 문서의 Resource Manager 버전을 보려면 [Azure Virtual Machines Resource Manager에서 SQL Server의 자동화된 백업](../sql/virtual-machines-windows-sql-automated-backup.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
자동화된 Backup을 사용하려면 다음 필수 조건을 고려하세요.

**운영 체제**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 버전**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016의 자동화된 백업은 리소스 관리자 가상 머신에서 지원됩니다. 자세한 내용은 [SQL Server 2016 Azure Virtual Machines의 자동화된 백업 v2(리소스 관리자)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)를 참조하세요.

**데이터베이스 구성**:

* 대상 데이터베이스는 전체 복구 모델을 사용해야 합니다.

**Azure PowerShell**:

* [최신 Azure PowerShell cmdlet을 설치합니다](/powershell/azure/overview).

**SQL Server IaaS 확장**:

* [SQL Server IaaS 확장을 설치합니다](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>설정
다음 표에서는 자동화된 Backup에 대해 구성할 수 있는 옵션을 설명합니다. 클래식 VM의 경우 이러한 설정을 구성하려면 PowerShell을 사용해야 합니다.

| 설정 | 범위(기본값) | 설명 |
| --- | --- | --- |
| **자동화된 Backup** |사용/사용 안 함(사용 안 함) |SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM에 대해 자동화된 Backup을 사용하거나 사용하지 않도록 설정합니다. |
| **보존 기간** |1-30일(30일) |백업 보존 기간(일 수)입니다. |
| **Storage 계정** |Azure 저장소 계정(지정된 VM에 대해 만든 저장소 계정) |Cloud Shell은 Azure File Storage를 활용하여 세션 간에 파일을 유지합니다. 모든 백업 파일을 저장하려면 컨테이너를 이 위치에 만듭니다. 백업 파일 명명 규칙에는 날짜, 시간 및 컴퓨터 이름이 포함됩니다. |
| **암호화** |사용/사용 안 함(사용 안 함) |암호화 사용 여부를 설정합니다. 암호화 기능을 사용하면 백업을 복원하는 데 사용되는 인증서가 동일한 명명 규칙을 사용하여 동일한 자동 백업 컨테이너에 지정한 저장소 계정에 배치됩니다. 암호가 변경되면 해당 암호를 사용하여 새 인증서가 생성되지만 이전 인증서도 이전 백업의 복원을 위해 유지됩니다. |
| **암호** |암호 텍스트(없음) |암호화 키의 암호입니다. 암호화를 사용하는 경우에만 필요합니다. 암호화된 백업을 복원하기 위해서는 올바른 암호 및 백업을 수행할 때 사용한 인증서가 있어야 합니다. |
| **시스템 데이터베이스 Backup** | 사용/사용 안 함(사용 안 함) | Master, Model 및 MSDB의 전체 백업 |
| **백업 일정 구성** | 수동/자동(자동) | 로그 증가에 따라 전체 및 로그 백업을 자동으로 수행하려면 **자동**을 선택합니다. 전체 및 로그 백업 일정을 지정하려면 **수동**을 선택합니다. |

## <a name="configuration-with-powershell"></a>PowerShell을 사용하여 구성
다음 PowerShell 예제에서는 기존 SQL Server 2014 VM에 대해 자동화된 Backup이 구성됩니다. **New-AzureVMSqlServerAutoBackupConfig** 명령은 $storageaccount 변수로 지정한 Azure 스토리지 계정에 백업을 저장하는 자동화된 백업 설정을 구성합니다. 이러한 백업은 10일 동안 보존됩니다. **Set-AzureVMSqlServerExtension** 명령은 지정된 Azure VM을 이러한 설정으로 업데이트합니다.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

암호화를 사용하려면 CertificatePassword 매개 변수에 대 한 암호(보안 문자열)와 함께 EnableEncryption 매개 변수를 전달 하도록 이전 스크립트를 수정합니다. 다음 스크립트를 사용하면 이전 예제의 자동화된 Backup 설정을 사용하고 암호화를 추가할 수 있습니다.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

자동 백업을 사용하지 않으려면 동일한 스크립트를 **-Enable** 매개 변수 없이 **New-AzureVMSqlServerAutoBackupConfig**에 대해 실행합니다. 설치와 마찬가지로 자동화된 Backup을 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> SQL Server IaaS 에이전트를 비활성화하고 제거해도 이전에 구성한 관리된 Backup 설정은 제거되지 않습니다. SQL Server IaaS 에이전트를 비활성화 또는 제거하기 전에 자동화된 Backup을 사용하지 않도록 설정해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
자동화된 Backup은 Azure VM에서 관리되는 Backup을 구성합니다. 따라서 [관리되는 Backup 설명서를 검토](https://msdn.microsoft.com/library/dn449496.aspx) 하여 동작 및 의미를 이해해야 합니다.

Azure VM의 SQL Server에 대한 추가적인 백업 및 복원 지침은 [Azure Virtual Machines에서 SQL Server의 백업 및 복원](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json) 항목을 참조하세요.

사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](../classic/sql-server-agent-extension.md)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

