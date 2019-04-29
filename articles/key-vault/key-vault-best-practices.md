---
title: Key Vault-Azure Key Vault를 사용 하는 모범 사례 | Microsoft Docs
description: 이 문서에서는 Key Vault를 사용 하는 최상의 방법 중 일부를 설명합니다
services: key-vault
author: mbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: ee4418700cec5de1dc404c3669dd5de315aab983
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640772"
---
# <a name="best-practices-to-use-key-vault"></a>Key Vault를 사용 하는 모범 사례

## <a name="control-access-to-your-vault"></a>자격 증명 모음에 대 한 액세스 제어

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하기 때문에 권한이 부여된 애플리케이션과 사용자만 허용하여 Key Vault에 대한 액세스를 보호해야 합니다. 이렇게 [문서](key-vault-secure-your-key-vault.md) 키 자격 증명 모음 액세스 모델의 개요를 제공 합니다. 여기서는 인증 및 권한 부여에 대해 설명하고 Key Vault 액세스의 보안을 유지하는 방법을 설명합니다.

자격 증명 모음에 대 한 액세스를 제어 하는 동안 제안 아래와 같습니다.
1. 구독, 리소스 그룹 및 키 자격 증명 모음 (RBAC)에 대 한 액세스 잠금
2. 모든 자격 증명 모음에 대 한 액세스 정책을 만들려면
3. 최소 권한 액세스 보안 주체를 사용 하 여 액세스 권한을 부여 하려면
4. 방화벽 설정 및 [VNET 서비스 끝점](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>별도 Key Vault 사용

환경 (개발, 프로덕션 전 및 프로덕션) 당 응용 프로그램별 자격 증명 모음을 사용 하는 것이 좋습니다. 이 환경에서 암호를 공유 하지는 또한 여 위반이 발생할 경우 위협을 줄일 수 있습니다.

## <a name="backup"></a>Backup

두어야 일반 다시은 하 [자격 증명 모음](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) 업데이트/삭제/만들기의 자격 증명 모음 개체입니다.

## <a name="turn-on-logging"></a>로깅 켜기

[로깅 켜기](key-vault-logging.md) 자격 증명 모음에 대 한 합니다. 경고를 설정할 수도 있습니다.

## <a name="turn-on-recovery-options"></a>복구 옵션 설정

1. 켜기 [일시 삭제](key-vault-ovw-soft-delete.md)합니다.
2. 일시 삭제가 설정 된 후에 자격 증명 모음 / 암호를 강제 삭제를 방지 하려는 경우 보호 제거를 켭니다.
