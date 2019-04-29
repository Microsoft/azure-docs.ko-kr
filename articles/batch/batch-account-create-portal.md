---
title: Azure Portal에서 계정 만들기 - Azure Batch | Microsoft Docs
description: 클라우드에서 대규모 병렬 작업을 실행하도록 Azure Portal에서 Azure Batch 계정을 만드는 방법에 대해 알아봅니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86747b72c436c4dac3bbf0a752fee4d24cb47f60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60722577"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal에서 Batch 계정 만들기

[Azure Portal][azure_portal]에 Azure Batch 계정을 만들고 계산 시나리오에 적합한 계정 속성을 선택하는 방법에 대해 알아봅니다. 액세스 키 및 계정 URL과 같은 중요한 계정 속성을 찾는 위치에 대해 알아봅니다.

Batch 계정 및 시나리오에 대한 배경은 [기능 개요](batch-api-basics.md)를 참조하세요.

## <a name="create-a-batch-account"></a>Batch 계정 만들기

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. [Azure Portal][azure_portal]에 로그인합니다.

1. **리소스 만들기** > **계산** > **Batch Service**를 선택합니다.

    ![Marketplace에서 Batch][marketplace_portal]

1. **새 Batch 계정** 설정을 입력합니다. 다음 세부 정보를 참조하세요.

    ![Batch 계정 만들기][account_portal]

    a. **구독**: Batch 계정을 만들 구독입니다. 하나의 구독만 보유하는 경우 기본적으로 선택됩니다.

    b. **리소스 그룹**: 새 Batch 계정에 대한 기존 리소스 그룹을 선택하거나 필요에 따라 새 리소스 그룹을 만듭니다.

    다. **계정 이름**: 선택한 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다(아래 **위치** 참조). 계정 이름은 소문자 또는 숫자만 포함할 수 있으며 길이는 3-24자여야 합니다.

    d. **위치**: Batch 계정을 만들 Azure 지역입니다. 구독 및 리소스 그룹에서 지원하는 지역만 옵션으로 표시됩니다.

    e. **스토리지 계정**: Batch 계정과 연결하는 Azure Storage 계정(선택 사항)입니다. 최상의 성능을 위해 범용 v2 스토리지 계정이 권장됩니다. Batch의 모든 스토리지 계정 옵션은 [Batch 기능 개요](batch-api-basics.md#azure-storage-account)를 참조하세요. 포털에서 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만듭니다.

      ![저장소 계정 만들기][storage_account]

    f. **풀 할당 모드**: **고급** 설정 탭에서 풀 할당 모드를 **Batch 서비스** 또는 **사용자 구독**으로 지정할 수 있습니다. 대부분의 시나리오의 경우 기본 **Batch 서비스**를 수락합니다.

      ![Batch 풀 할당 모드][pool_allocation]

1. **만들기**를 선택하여 계정을 만듭니다.

## <a name="view-batch-account-properties"></a>Batch 계정 속성 보기

계정이 만들어지면 계정을 선택하여 해당 설정 및 속성에 액세스합니다. 왼쪽 메뉴를 사용하여 모든 계정 설정 및 속성에 액세스할 수 있습니다.

![Azure Portal에서 배치 계정 페이지][account_blade]

* **Batch 계정 이름, URL 및 키**: [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development)를 사용하여 애플리케이션을 개발하는 경우 Batch 리소스에 액세스하려면 계정 URL 및 키가 필요합니다. (Batch는 Azure Active Directory 인증도 지원합니다.)

    배치 계정 액세스 정보를 보려면 **키**를 선택합니다.

    ![Azure Portal에서 Batch 계정 키][account_keys]

* 배치 계정에 연결된 저장소 계정의 이름 및 키를 보려면 **Storage 계정**을 선택합니다.

* 배치 계정에 적용되는 리소스 할당량을 보려면 **할당량**을 선택합니다. 자세한 내용은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

## <a name="additional-configuration-for-user-subscription-mode"></a>사용자 구독 모드에 대한 추가 구성

사용자 구독 모드에서 배치 계정 만들기를 선택하면 계정을 만들기 전에 다음과 같은 추가 단계를 수행합니다.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch에서 구독에 액세스하도록 허용(일회성 작업)

사용자 구독 모드에서 첫 번째 Batch 계정을 만들 때 구독을 Batch 계정에 등록합니다. (이전에 이 작업을 수행한 경우 다음 섹션으로 건너뜁니다.)

1. [Azure Portal][azure_portal]에 로그인합니다.

1. **모든 서비스** > **구독**을 선택하고, 배치 계정에 사용할 구독을 선택합니다.

1. **구독** 페이지에서 **리소스 공급자**를 선택하고, **Microsoft.Batch**를 검색합니다. **Microsoft.Batch** 리소스 공급자가 구독에 등록되었는지 확인합니다. 등록되지 않았으면 **등록** 링크를 선택합니다.

    ![Microsoft.Batch 공급자 등록][register_provider]

1. **구독** 페이지에서 **액세스 제어(IAM)** > **역할 할당** > **역할 할당 추가**를 차례로 선택합니다.

    ![구독 액세스 제어][subscription_access]

1. **역할 할당 추가** 페이지에서 **기여자** 역할을 선택하고 Batch API를 검색합니다. API를 찾을 때까지 다음 문자열 각각을 검색합니다.
    1. **MicrosoftAzureBatch**
    1. **Microsoft Azure Batch** - 최신 Azure AD 테넌트에서는 이 이름을 사용할 수도 있습니다.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864**는 Batch API에 대한 ID입니다.

1. Batch API를 찾았으면 해당 API를 선택하고 **저장**을 선택합니다.

    ![Batch 권한 추가][add_permission]

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

사용자 구독 모드에서 Azure 키 자격 증명 모음은 만들려는 Batch 계정과 동일한 리소스 그룹에 속해야 합니다. Batch를 [사용할 수 있고](https://azure.microsoft.com/regions/services/) 구독에서 지원하는 지역에 리소스 그룹이 있는지 확인합니다.

1. [Azure Portal][azure_portal]에서 **새로 만들기** > **보안** > **Key Vault**를 차례로 선택합니다.

1. **Key Vault 만들기** 페이지에서 키 자격 증명 모음의 이름을 입력하고, Batch 계정에 사용할 지역에 리소스 그룹을 만듭니다. 나머지 설정은 기본값으로 그대로 두고 **만들기**를 선택합니다.

사용자 구독 모드에서 Batch 계정을 만들 때 키 자격 증명 모음에 대한 리소스 그룹을 사용하고, 풀 할당 모드로 **사용자 구독**을 지정하고, 키 자격 증명 모음을 선택합니다.

### <a name="configure-subscription-quotas"></a>구독 할당량 구성

코어 할당량은 사용자 구독 배치 계정에 기본적으로 설정되지 않습니다. 표준 Batch 코어 할당량은 사용자 구독 모드의 계정에 적용되지 않으므로 코어 할당량을 수동으로 설정해야 합니다.

1. [Azure Portal][azure_portal]에서 사용자 구독 모드 배치 계정을 선택하여 해당 설정과 속성을 표시합니다.

1. 왼쪽 메뉴에서 **할당량**을 선택하여 배치 계정과 연결된 코어 할당량을 보고 구성합니다.

사용자 구독 모드 코어 할당량에 대한 자세한 내용은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

## <a name="other-batch-account-management-options"></a>다른 Batch 계정 관리 옵션

Azure Portal을 사용하는 것 외에도 다음을 포함하는 도구로 Batch 계정을 만들고 관리할 수 있습니다.

* [Batch PowerShell cmdlets](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch 관리 .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>다음 단계

* Batch 서비스의 개념 및 기능에 대한 자세한 내용은 [Batch 기능 개요](batch-api-basics.md)를 참조하세요. 이 문서에서는 풀, 계산 노드, 작업 및 태스크 등의 기본 Batch 리소스에 대해 설명하고 대규모 계산 워크로드를 위한 서비스 기능 개요를 제공합니다.
* [Batch .NET 클라이언트 라이브러리](quick-run-dotnet.md) 또는 [Python](quick-run-python.md)을 사용하여 Batch 지원 응용 프로그램 개발에 대한 기본 사항을 알아봅니다. 이러한 빠른 시작에서는 Batch 서비스를 사용하여 여러 계산 노드에서 워크로드를 실행하는 애플리케이션 예제를 단계별로 안내하며, Azure Storage를 사용하여 워크로드 파일을 준비하고 검색하는 방법을 설명합니다.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
