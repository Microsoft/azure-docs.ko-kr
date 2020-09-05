---
title: 'PowerShell: BYOK (BYOK) TDE를 사용 하도록 설정'
titleSuffix: Azure SQL Managed Instance
description: PowerShell을 사용 하 여 미사용 암호화를 위해 TDE (BYOK) 투명한 데이터 암호화 (BYOK)를 사용 하 여 시작 하도록 Azure SQL Managed Instance를 구성 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 08/25/2020
ms.openlocfilehash: c812dfa0be3e5ced40b7f347639aeb2540b75351
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871533"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 고유한 키를 사용 하 여 SQL Managed Instance에서 투명한 데이터 암호화

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

이 PowerShell 스크립트 예제에서는 Azure Key Vault의 키를 사용 하 여 Azure SQL Managed Instance에 대 한 고객 관리 키를 사용 하 여 TDE (투명한 데이터 암호화)를 구성 합니다. 이를 종종 TDE에 대 한 BYOK (BYOK) 시나리오 라고 합니다. 자세한 내용은 [고객 관리 키를 사용 하 여 AZURE SQL 투명한 데이터 암호화](../../database/transparent-data-encryption-byok-overview.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

- 기존 관리형 인스턴스. [PowerShell을 사용 하 여 관리 되는 인스턴스 만들기를](create-configure-managed-instance-powershell.md)참조 하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 사용 하거나 Azure Cloud Shell을 사용 하려면 Azure PowerShell 2.3.2 이상 버전이 필요 합니다. 업그레이드 해야 하는 경우에는 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조 하거나 아래 샘플 스크립트를 실행 하 여 현재 사용자에 대 한 모듈을 설치 합니다.

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

SQL Managed Instance에 대한 추가 PowerShell 스크립트 샘플은 [Azure SQL Managed Instance PowerShell 스크립트](../../database/powershell-script-content-guide.md)에서 찾을 수 있습니다.
