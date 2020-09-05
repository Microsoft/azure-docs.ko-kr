---
title: Key Vault 사용에 대 한 모범 사례-Azure Key Vault | Microsoft Docs
description: 액세스 제어, 개별 키 자격 증명 모음을 사용 하는 경우, 백업, 로깅 및 복구 옵션을 포함 하 여 Azure Key Vault에 대 한 모범 사례에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cec3ad4e113fd6ee3f4e30ad2a6877b886a958e0
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88189890"
---
# <a name="best-practices-to-use-key-vault"></a>Key Vault 사용에 대 한 모범 사례

## <a name="control-access-to-your-vault"></a>자격 증명 모음에 대 한 액세스 제어

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하기 때문에 권한이 부여된 애플리케이션과 사용자만 허용하여 Key Vault에 대한 액세스를 보호해야 합니다. 이 [문서](secure-your-key-vault.md) 에서는 Key Vault 액세스 모델의 개요를 제공 합니다. 여기서는 인증 및 권한 부여에 대해 설명하고 Key Vault 액세스의 보안을 유지하는 방법을 설명합니다.

자격 증명 모음에 대 한 액세스를 제어 하는 동안 제안 사항은 다음과 같습니다.
1. 구독, 리소스 그룹 및 키 자격 증명 모음에 대 한 액세스 잠금 (RBAC)
2. 모든 자격 증명 모음에 대 한 액세스 정책 만들기
3. 최소 권한 액세스 보안 주체를 사용 하 여 액세스 권한 부여
4. 방화벽 및 [VNET 서비스 끝점](overview-vnet-service-endpoints.md) 설정

## <a name="use-separate-key-vault"></a>별도의 Key Vault 사용

환경 별로 응용 프로그램당 자격 증명 모음을 사용 하는 것이 좋습니다 (개발, 프로덕션 전 및 프로덕션). 이를 통해 환경에서 비밀을 공유 하지 않으며 위반 시 위협이 줄어듭니다.

## <a name="backup"></a>Backup

자격 증명 모음 내에서 개체의 업데이트/삭제/만들기에 대 한 자격 증명 모음을 정기적으로 백업 해야 합니다.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell 백업 명령

* [인증서 백업](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [백업 키](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [백업 비밀](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Azure CLI 백업 명령

* [인증서 백업](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [백업 키](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [백업 비밀](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>로깅 설정

자격 증명 모음에 대 한 [로깅을 설정](logging.md) 합니다. 또한 경고를 설정 합니다.

## <a name="turn-on-recovery-options"></a>복구 옵션 설정

1. [일시 삭제](soft-delete-overview.md)를 켭니다.
2. 일시 삭제가 설정 된 후에도 비밀/자격 증명 모음을 강제로 삭제 하지 않도록 보호 하려면 제거 보호를 설정 합니다.
