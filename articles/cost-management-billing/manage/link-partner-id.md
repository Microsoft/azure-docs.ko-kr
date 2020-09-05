---
title: 파트너 ID에 Azure 계정 연결
description: 고객의 리소스를 관리하기 위해 사용하는 사용자 계정에 파트너 ID를 연결하여 Azure 고객 관리를 추적합니다.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 07/24/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: 8f3e4762b0c0286a47b407595cf73b66bef8d750
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682844"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Azure 계정에 파트너 ID 연결

Microsoft 파트너는 고객이 Microsoft 제품을 사용하여 비즈니스 및 업무 목표를 달성할 수 있도록 하는 서비스를 제공합니다. 고객을 대신하여 Azure 서비스를 관리, 구성 및 지원하는 경우 파트너 사용자는 고객 환경에 액세스할 수 있어야 합니다. 파트너는 파트너 관리 링크(PAL)를 사용하여 파트너 네트워크 ID를 서비스 배달에 사용되는 자격 증명과 연결할 수 있습니다.

PAL을 통해 Microsoft는 Azure 고객 성공을 주도하는 파트너를 식별하고 인식할 수 있습니다. Microsoft는 계정의 권한(Azure 역할) 및 범위(구독, 리소스 그룹, 리소스)를 기반으로 영향과 Azure 사용 수익을 귀하 조직에 귀속시킬 수 있습니다.

## <a name="get-access-from-your-customer"></a>고객의 액세스 권한 가져오기

파트너 ID를 연결하려면 먼저 고객이 다음 옵션 중 하나를 사용하여 해당 Azure 리소스에 대한 액세스 권한을 사용자에게 부여해야 합니다.

- **게스트 사용자**: 고객은 사용자를 게스트 사용자로 추가하고 Azure 역할을 할당할 수 있습니다. 자세한 내용은 [다른 디렉터리에서 게스트 사용자 추가](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 참조하세요.

- **디렉터리 계정**: 고객은 자신의 디렉터리에 사용자 계정을 만들고 Azure 역할을 할당할 수 있습니다.

- **서비스 주체**: 고객은 해당 디렉터리에서 조직의 앱 또는 스크립트를 추가하고 Azure 역할을 할당할 수 있습니다. 앱 또는 스크립트의 ID를 서비스 주체라고 합니다.

- **Azure Lighthouse**: 사용자가 테넌트 내에서 작업할 수 있도록 고객이 구독(또는 리소스 그룹)을 위임할 수 있습니다. 자세한 내용은 [Azure 위임 리소스 관리](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)를 참조하세요.

## <a name="link-to-a-partner-id"></a>파트너 ID에 연결

고객의 리소스에 액세스할 수 있는 경우 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 MPN ID(Microsoft 파트너 네트워크 ID)를 사용자 ID 또는 서비스 주체에 연결합니다. 각 고객 테넌트에서 파트너 ID를 연결합니다.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure Portal을 사용하여 새 파트너 ID에 연결

1. Azure Portal에서 [파트너 ID에 연결](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)로 이동합니다.

2. Azure Portal에 로그인합니다.

3. Microsoft 파트너 ID를 입력합니다. 파트너 ID는 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID입니다.

   ![파트너 ID에 연결을 보여 주는 스크린샷](./media/link-partner-id/link-partner-id01.png)

4. 다른 고객의 파트너 ID에 연결하려면 디렉터리를 전환합니다. **디렉터리 전환** 아래에서 디렉터리를 선택합니다.

   ![디렉터리를 전환을 보여 주는 스크린샷](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell을 사용하여 새 파트너 ID에 연결

1. [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell 모듈을 설치합니다.

2. 사용자 계정 또는 서비스 주체를 사용하여 고객의 테넌트에 로그인합니다. 자세한 내용은 [PowerShell로 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)을 참조하세요.

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. 새 파트너 ID에 연결합니다. 파트너 ID는 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID입니다.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>연결된 파트너 ID 가져오기
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>연결된 파트너 ID 업데이트
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>연결된 파트너 ID 삭제
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Azure CLI를 사용하여 새 파트너 ID에 연결
1. Azure CLI 확장을 설치합니다.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. 사용자 계정 또는 서비스 주체를 사용하여 고객의 테넌트에 로그인합니다. 자세한 내용은 [Azure CLI로 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조하세요.

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. 새 파트너 ID에 연결합니다. 파트너 ID는 조직의 [Microsoft 파트너 네트워크](https://partner.microsoft.com/) ID입니다.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>연결된 파트너 ID 가져오기
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>연결된 파트너 ID 업데이트
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>연결된 파트너 ID 삭제
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>다음 단계

[Microsoft 파트너 커뮤니티](https://aka.ms/PALdiscussion)의 토론에 참여하여 업데이트를 받거나 의견을 보냅니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**파트너 ID를 연결할 수 있는 사용자는 누구인가요?**

고객의 Azure 리소스를 관리하는 파트너 조직의 모든 사용자는 파트너 ID를 계정에 연결할 수 있습니다.

**파트너 ID가 연결된 후에 파트너 ID를 변경할 수 있나요?**

예. 연결된 파트너 ID를 변경하거나, 추가하거나, 제거할 수 있습니다.

**사용자가 둘 이상의 고객 테넌트에 계정을 보유한 경우에 어떻게 되나요?**

파트너 ID와 계정 간의 연결은 고객 테넌트 각각에 수행됩니다. 각 고객 테넌트에서 파트너 ID를 연결합니다.

**다른 파트너 또는 고객이 파트너 ID에 대한 연결을 편집하거나 제거할 수 있나요?**

링크는 사용자 계정 수준에서 연결됩니다. 사용자만이 파트너 ID에 대한 연결을 편집하고 제거할 수 있습니다. 고객 및 다른 파트너는 파트너 ID에 대한 연결을 변경할 수 없습니다.


**회사에 여러 개의 MPN ID가 있는 경우 어떤 MPN ID를 사용해야 하나요?**

파트너 위치 계정 및 연결된 MPN ID는 파트너 ID를 연결하는 데 사용해야 합니다.  [파트너 계정](https://docs.microsoft.com/partner-center/account-structure)에 대한 자세한 정보

**연결된 파트너 ID에 대한 영향을 받는 수익 보고는 어디에서 찾을 수 있나요?**

클라우드 제품 성능 보고는 [내 인사이트 대시보드](https://partner.microsoft.com/membership/reports/myinsights)의 파트너 센터에서 파트너에게 제공됩니다. 파트너 연결 형식으로 파트너 관리 링크를 선택해야 합니다.

**보고서에서 고객을 볼 수 없는 이유는 무엇입니까?**

다음과 같은 이유로 인해 보고서에서 고객을 볼 수 없습니다.

1. 연결된 사용자 계정에는 고객 Azure 구독 또는 리소스에 대한 [역할 기반 액세스](https://docs.microsoft.com/azure/role-based-access-control/overview) 권한이 없습니다.

2. 사용자에게 [역할 기반 액세스](https://docs.microsoft.com/azure/role-based-access-control/overview) 권한이 있는 Azure 구독에는 사용 권한이 없습니다.

**연결 파트너 ID는 Azure Stack과 작동하나요?**

예, Azure Stack에 대한 파트너 ID를 연결할 수 있습니다.

**회사에서 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview)를 사용하여 고객 리소스에 액세스하는 경우 내 파트너 ID를 어떻게 연결하나요?**

[관리형 서비스 제품을 Azure Marketplace에 게시](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers)하여 고객을 Azure 위임 리소스 관리에 온보딩하면 MPN ID가 자동으로 연결됩니다. [Azure Resource Manager 템플릿을 배포하여 고객을 온보딩](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer)하는 경우 MPN(Microsoft 파트너 네트워크) ID를 각 온보딩된 각 구독에 액세스할 수 있는 하나 이상의 사용자 계정과 연결해야 합니다. 이 작업은 서비스 공급자 테넌트에서 수행해야 합니다. 간단히 하기 위해, 테넌트에서 MPN ID와 연결된 서비스 주체 계정을 만들고 온보딩한 모든 고객에 대한 읽기 권한자 액세스 권한을 이 계정에 부여하는 것이 좋습니다. 이 예제에서는 RBAC 판독기 역할이 사용되며 파트너 획득 크레딧을 사용할 수 없는 역할 중 하나입니다. 역할에 대한 자세한 내용 [파트너 획득 크레딧에 대한 역할 및 권한](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3QuW2)을 참조하세요.


**고객에게 PAL(파트너 관리자 링크)을 어떻게 설명하나요?**

PAL(파트너 관리자 링크)을 통해 Microsoft는 고객이 비즈니스 목표를 달성하고 클라우드에서 가치를 실현할 수 있도록 돕는 파트너를 식별하고 인식할 수 있습니다. 고객은 먼저 Azure 리소스에 대한 파트너 액세스를 제공해야 합니다. 액세스 권한이 부여되면 파트너의 MPN ID(Microsoft 파트너 네트워크 ID)가 연결됩니다. 이 연결을 통해 Microsoft는 IT 서비스 공급자의 에코시스템을 이해하고 일반적인 고객을 가장 잘 지원하는 데 필요한 도구와 프로그램을 구체화할 수 있습니다.

**PAL은 어떤 데이터를 수집하나요?**

기존 자격 증명에 대한 PAL 연결은 새로운 고객 데이터를 Microsoft에 제공하지 않습니다. 파트너는 고객의 Azure 환경에 적극적으로 참여하는 Microsoft에 원격 분석을 제공하기만 하면 됩니다. Microsoft는 고객이 파트너에게 제공하는 계정 권한(Azure 역할) 및 범위(관리 그룹, 구독, 리소스 그룹, 리소스)를 기반으로 고객 환경에서 영향과 Azure 사용 수익을 파트너 조직에 귀속시킬 수 있습니다. 

**이는 고객의 Azure 환경 보안에 영향을 주나요?**

PAL 연결은 이미 프로비저닝된 자격 증명에 파트너의 MPN ID만 추가하고, 권한(Azure 역할)을 변경하거나 파트너 또는 Microsoft에 추가 Azure 서비스 데이터를 제공하지 않습니다. 
