---
title: Azure Key Vault 고객 데이터 기능 - Azure Key Vault | Microsoft Docs
description: 자격 증명 모음, 키, 비밀, 인증서 및 관리형 스토리지 계정의 생성 또는 업데이트 과정에서 Azure Key Vault에서 수신하는 고객 데이터에 관해 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4d45c019a6ba073d7553c09784736959faf89d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749783"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault 고객 데이터 기능

Azure Key Vault는 자격 증명 모음, 관리형 HSM 풀, 키, 비밀, 인증서 및 관리형 스토리지 계정의 생성 또는 업데이트 과정에서 고객 데이터를 수신합니다. 이 고객 데이터는 Azure Portal과 REST API를 통해 직접 표시됩니다. 고객 데이터는 데이터를 포함하는 개체를 업데이트하거나 삭제하여 편집 또는 삭제될 수 있습니다.

사용자 또는 애플리케이션에서 Key Vault에 액세스하는 경우 시스템 액세스 로그가 생성됩니다. 고객은 Azure Insights를 통해 자세한 액세스 로그를 사용할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>고객 데이터 식별

다음 정보는 Azure Key Vault 내에서 고객 데이터를 식별합니다.

- Azure Key Vault에 대한 액세스 정책은 사용자, 그룹 또는 애플리케이션을 나타내는 개체 ID를 포함합니다.
- 인증서 주체는 이메일 주소 또는 다른 사용자 또는 조직 식별자를 포함할 수 있습니다.
- 인증서 연락처는 사용자 이메일 주소, 이름 또는 전화 번호를 포함할 수 있습니다.
- 인증서 발급자는 이메일 주소, 이름, 전화 번호, 계정 자격 증명 및 조직 세부 정보를 포함할 수 있습니다.
- 임의 태그를 Azure Key Vault의 개체에 적용할 수 있습니다. 이러한 개체는 자격 증명 모음, 키, 비밀, 인증서 및 스토리지 계정을 포함합니다. 사용된 태그는 개인 데이터를 포함할 수 있습니다.
- Azure Key Vault 액세스 로그는 개체 ID, [UPN](../../active-directory/hybrid/plan-connect-userprincipalname.md) 및 각 REST API 호출에 대한 IP 주소를 포함합니다.
- Azure Key Vault 진단 로그는 개체 ID 및 REST API 호출에 대한 IP 주소를 포함할 수 있습니다.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

자격 증명 모음, 키, 비밀, 인증서 및 관리되는 스토리지 계정을 만드는 데 사용된 동일한 REST API, 포털 환경 및 SDK는 이러한 개체를 업데이트 및 삭제할 수도 있습니다.

일시 삭제를 사용하면 삭제 후 90일 동안 삭제된 데이터를 복구할 수 있습니다. 일시 삭제를 사용할 경우 제거 작업을 수행하여 90일 보존 기간 전에 데이터를 영구적으로 삭제할 수 있습니다. 자격 증명 모음 또는 구독이 제거 작업을 차단하도록 구성된 경우 예약된 보존 기간이 경과될 때까지 데이터를 영구적으로 삭제할 수 없습니다.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

자격 증명 모음, 키, 비밀, 인증서 및 관리되는 스토리지 계정을 만드는 데 사용된 동일한 REST API, 포털 환경 및 SDK를 통해 이러한 개체를 보고 내보낼 수도 있습니다.

Azure Key Vault 액세스 로깅은 각 REST API 호출에 대한 로그를 생성하도록 설정할 수 있는 선택적 기능입니다. 이러한 로그는 조직의 요구 사항을 충족하는 보존 정책을 적용하는 구독의 스토리지 계정에 전송됩니다.

사용자 개인 정보 보호 포털에서 내보내기 요청을 만들어 개인 데이터를 포함하는 Azure Key Vault 진단 로그를 검색할 수 있습니다. 테넌트 관리자가 이 요청을 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 로깅](logging.md)

- [Azure Key Vault 일시 삭제 개요](./key-vault-recovery.md)

- [Azure Key Vault 키 작업](/rest/api/keyvault/key-operations)

- [Azure Key Vault 비밀 작업](/rest/api/keyvault/secret-operations)

- [Azure Key Vault 인증서 및 정책](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault 스토리지 계정 작업](/rest/api/keyvault/storage-account-key-operations)