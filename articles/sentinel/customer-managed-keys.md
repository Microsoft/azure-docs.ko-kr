---
title: Azure Sentinel에서 고객 관리형 키 설정| Microsoft Docs
description: Azure Sentinel에서 CMK(고객 관리형 키)를 설정하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/19/2020
ms.author: yelevin
ms.openlocfilehash: 58936066abcbe4c3f9fcfad78bf914c74079aa95
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141791"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Azure Sentinel 고객 관리형 키 설정


이 문서에서는 Azure Sentinel에 대한 CMK(고객 관리형 키)를 구성하는 단계 및 배경 정보를 제공합니다. CMK를 사용하면 사용자가 만들거나 소유한 Azure Key Vault 키로 Azure Sentinel에 저장되거나 전송된 모든 데이터를 모든 관련 스토리지 리소스에서 암호화할 수 있습니다.

> [!NOTE]
> -   Azure Sentinel CMK 기능은 **신규** 고객에게만 제공되며 이 기능에 대한 액세스는 Azure 기능 등록을 통해 제어됩니다. azuresentinelCMK@microsoft.com에 문의하여 액세스 권한을 요청할 수 있으며, 자격이 되면 보류 중인 요청이 승인됩니다.
> -   Azure Sentinel CMK 기능은 미국 동부, 미국 서부 2 및 미국 중남부 지역에서만 사용할 수 있습니다.
> -   CMK 기능은 매일 1TB 이상을 전송하는 고객만 사용할 수 있습니다. Microsoft에 적용하여 Azure 구독의 CMK를 프로비저닝할 때 가격 책정에 대한 추가 정보를 받게 됩니다. [Log Analytics 가격 책정](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters)에 대해 자세히 알아보세요.

## <a name="how-cmk-works"></a>CMK 작동 방법 

Azure Sentinel 솔루션은 Log Analytics 등을 포함하여 로그 수집 및 기능에 여러 스토리지 리소스를 사용합니다. Azure Sentinel CMK 구성의 일부로 관련 스토리지 리소스에서도 CMK 설정을 구성해야 합니다. Log Analytics 이외의 스토리지 리소스에 저장된 데이터도 암호화됩니다.

[CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-cmk-overview)에 대해 자세히 알아보세요.

> [!NOTE]
> Azure Sentinel에서 CMK를 사용하도록 설정하는 경우 CMK를 지원하지 않는 공개 미리 보기 기능은 사용되지 않습니다.

## <a name="enable-cmk"></a>CMK 사용 

CMK를 프로비저닝하려면 다음 단계를 수행합니다. 

1.  Azure Key Vault를 만들고 키를 저장합니다.

2.  Log Analytics 작업 영역에 CMK를 사용하도록 설정합니다.

3.  Cosmos DB에 등록합니다.

4.  Azure Key Vault 인스턴스에 액세스 정책을 추가합니다.

5.  Azure Sentinel에서 CMK를 사용하도록 설정합니다.

6.  Azure Sentinel을 사용하도록 설정합니다.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>1단계: Azure Key Vault 만들기 및 키 저장

1.  [Azure Key Vault 리소스를 만든](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910) 다음 데이터 암호화에 사용할 키를 생성하거나 가져옵니다.
    > [!NOTE]
    >  키와 액세스를 보호하기 위해 Azure Key Vault를 복구 가능으로 구성해야 합니다.

1.  [복구 옵션 설정:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   [일시 삭제](../key-vault/general/soft-delete-overview.md)가 설정되어 있는지 확인합니다.

    -   일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제하지 않도록 보호하려면 [제거 보호](../key-vault/general/soft-delete-overview.md#purge-protection)를 설정합니다.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2단계: Log Analytics 작업 영역에서 CMK 사용

다음 단계에서 Azure Sentinel 작업 영역으로 사용할 CMK 작업 영역을 만들기 위해 [Azure Monitor 고객 관리형 키 구성](../azure-monitor/platform/customer-managed-keys.md)의 지침을 따릅니다.

### <a name="step-3-register-for-cosmos-db"></a>3단계: Cosmos DB 등록

Azure Sentinel은 추가 스토리지 리소스인 Cosmos DB와 함께 작동합니다. Cosmos DB에 등록해야 합니다.

Cosmos DB 지침에 따라 Azure 구독에 대한 [Azure Cosmos DB 리소스 공급자를 등록](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)합니다.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4단계: Azure Key Vault 인스턴스에 액세스 정책 추가

Cosmos DB에서 Azure Key Vault 인스턴스에 대한 액세스를 추가해야 합니다. Cosmos DB 지침에 따라 Azure Cosmos DB 보안 주체를 사용하여 [Azure Key Vault 인스턴스에 액세스 정책을 추가](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)합니다.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>5단계: Azure Sentinel에서 CMK 사용

Azure Sentinel CMK 기능은 Azure 제품 그룹에서 직접 액세스 권한을 받은 신규 고객에게만 제공됩니다. Microsoft의 연락처에 문의하여 Azure Sentinel 팀의 승인을 받고 솔루션에서 CMK를 사용하도록 설정합니다.

승인을 받으면 CMK 기능을 사용하도록 다음 정보를 입력하라는 메시지가 표시됩니다.

-  CMK를 사용하도록 설정하려는 작업 영역 ID

-  Key Vault URL: 키의 "키 식별자"를 마지막 슬래시까지 복사 합니다.  
    

    ![키 식별자](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel 팀에서 제공된 작업 영역에 Azure Sentinel CMK 기능을 사용하도록 설정합니다.

-  이 기능을 사용하도록 승인한 Azure Sentinel 제품 팀에서 확인합니다. 계속하기 전에 승인을 받아야 합니다.

### <a name="step-6-enable-azure-sentinel"></a>6단계: Azure Sentinel을 사용하도록 설정합니다.


Azure Portal로 이동하여 CMK를 설정한 작업 영역에서 Azure Sentinel을 사용하도록 설정합니다. 자세한 내용은 [Azure Sentinel 온보딩](quickstart-onboard.md)을 참조하세요.

## <a name="key-encryption-key-revocation-or-deletion"></a>키 암호화 키 해지 또는 삭제


사용자가 키 암호화 키를 삭제하거나 1시간 이내에 Azure Sentinel에 대한 액세스 권한을 제거하여 키 암호화 키를 해지하는 경우 Azure Sentinel은 변경 내용을 적용하여 데이터를 더 이상 사용할 수 없는 것처럼 동작합니다. 이 시점에서는 데이터 수집, 영구 구성 변경 및 인시던트 생성과 같은 영구 스토리지 리소스를 사용하는 모든 작업을 수행할 수 없습니다. 이전에 저장된 데이터는 삭제되지 않지만 액세스할 수 없는 상태가 됩니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 의해 제어되며 해당 정책에 따라 제거됩니다.

암호화 키가 해지되거나 삭제된 후에는 계정 삭제 작업만 수행할 수 있습니다.

해지 후 액세스가 복원되는 경우 Azure Sentinel은 1시간 이내에 데이터에 대한 액세스를 복원합니다.

Azure Monitor에서 이 기능이 작동하는 방식에 대한 자세한 내용은 [Azure Monitor CMK 해지](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)를 참조하세요.

## <a name="key-encryption-key-rotation"></a>키 암호화 키 순환


Azure Sentinel 및 Log Analytics는 키 순환을 지원합니다. 사용자가 Key Vault에서 키 순환을 수행할 때 Azure Sentinel은 1시간 내에 새 키를 지원합니다.

Key Vault에서 키의 새 버전을 만들어 키 순환을 수행할 수 있습니다.

![키 회전](./media/customer-managed-keys/key-rotation.png)

24시간 후 또는 Azure Key Vault 감사 로그에 이전 버전을 사용하는 작업이 더 이상 나타나지 않으면 이전 버전의 키를 사용하지 않도록 설정할 수 있습니다.

Azure Sentinel 및 Log Analytics에서 동일한 키를 사용하는 경우 새 Azure Key Vault 키 버전으로 Log Analytics의 클러스터 리소스를 명시적으로 업데이트해야 하는 키 순환을 수행해야 합니다. 자세한 내용은 [Azure Monitor CMK 회전](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서 Azure Sentinel에서 고객 관리형 키를 설정하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

