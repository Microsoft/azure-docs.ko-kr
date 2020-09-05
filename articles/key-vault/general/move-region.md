---
title: 자격 증명 모음을 다른 지역으로 이동 - Azure Key Vault | Microsoft Docs
description: 이 문서에서는 키 자격 증명 모음을 다른 지역으로 이동하는 방법에 대한 지침을 제공합니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 7d58cd26b6e4ca77da98f8c2f82dbdb481ccbb50
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585755"
---
# <a name="move-an-azure-key-vault-across-regions"></a>지역 간에 Azure 키 자격 증명 모음 이동

Azure Key Vault는 한 지역에서 다른 지역으로 키 자격 증명 모음을 이동할 수 있는 리소스 이동 작업을 지원하지 않습니다. 이 문서에서는 비즈니스 자격 증명 모음을 다른 지역으로 이동해야 하는 조직에게 필요한 해결 방법을 설명합니다. 각 해결 방법 옵션에는 제한이 있습니다. 이러한 해결 방법을 프로덕션 환경에 적용하기 전에 해당 환경에 미치는 영향을 이해하는 것이 중요합니다.

키 자격 증명 모음을 다른 지역으로 이동하려면 해당하는 다른 지역에 키 자격 증명 모음을 만든 다음, 기존 키 자격 증명 모음의 각 개별 비밀을 새 주요 자격 증명 모음에 수동으로 복사합니다. 다음 두 가지 옵션 중 하나를 사용하여 이 작업을 수행할 수 있습니다.

## <a name="design-considerations"></a>디자인 고려 사항

시작하기 전에 다음과 같은 개념을 염두에 두어야 합니다.

* 키 자격 증명 모음 이름은 전역적으로 고유합니다. 자격 증명 모음 이름을 다시 사용할 수 없습니다.
* 새 키 자격 증명 모음에서 액세스 정책 및 네트워크 구성 설정을 다시 구성해야 합니다.
* 새 키 자격 증명 모음에서 일시 삭제 및 제거 방지를 다시 구성해야 합니다.
* 백업 및 복원 작업은 자동 회전 설정을 유지하지 않습니다. 설정을 다시 구성해야 할 수도 있습니다.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>옵션 1: 키 자격 증명 모음 백업 및 복원 명령 사용

백업 명령을 사용하여 각 개별 비밀, 키 및 인증서를 자격 증명 모음에 백업할 수 있습니다. 비밀은 암호화된 Blob으로 다운로드됩니다. 그런 다음, Blob을 새 주요 자격 증명 모음으로 복원할 수 있습니다. 명령 목록은 [Azure Key Vault 명령](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)을 참조하세요.

백업 및 복원 명령을 사용할 때는 두 가지 제한 사항이 있습니다.

* 키 자격 증명 모음을 한 지역에 백업하고 다른 지역에 복원할 수는 없습니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조하세요.

* 백업 명령은 각 비밀의 모든 버전을 백업합니다. 이전 버전이 많은 비밀(10개 초과)을 사용하는 경우 요청 크기가 허용되는 최댓값을 초과하여 작업이 실패할 수 있습니다.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>옵션 2: 수동으로 키 자격 증명 모음 비밀 다운로드 및 업로드

특정 비밀 유형을 수동으로 다운로드할 수 있습니다. 예를 들어 인증서를 PFX 파일로 다운로드할 수 있습니다. 이 옵션은 인증서와 같은 일부 비밀 유형에 대한 지리적 제한을 제거합니다. 모든 지역의 모든 키 자격 증명 모음에 PFX 파일을 업로드할 수 있습니다. 비밀은 암호로 보호되지 않은 형식으로 다운로드됩니다. 이동하는 동안 비밀을 보호해야 합니다.
