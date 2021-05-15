---
title: Azure AD Domain Services의 복제본 세트 개념 | Microsoft Docs
description: Azure Active Directory Domain Services에 있는 복제본 세트의 유형 및 ID 서비스를 필요로 하는 애플리케이션에 중복성을 제공하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: justinha
ms.openlocfilehash: 8bcd3ebef027ec72728be21b0fe1504236f553ba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058173"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대한 복제본 세트 개념 및 기능

Azure Active Directory Domain Services (Azure AD DS) 관리되는 도메인을 만들 때, 고유한 네임스페이스를 정의합니다. 이 네임스페이스는 *aaddscontoso.com* 같은 도메인 이름이며, 두 개의 DC(도메인 컨트롤러)가 선택한 Azure 지역에 배포됩니다. 이 DC 배포를 복제본 세트라고 합니다.

관리되는 도메인을 확장하여 Azure AD 테넌트마다 복제본 세트를 2개 이상 포함할 수 있습니다. Azure AD DS을 지원하는 Azure 지역의 피어링된 가상 네트워크에 복제본 세트를 추가할 수 있습니다. 다른 Azure 지역에 있는 추가 복제본 세트는 Azure 지역이 오프라인 상태가 될 때 레거시 애플리케이션에 대한 지리적 재해 복구를 제공합니다.

> [!NOTE]
> 복제본 세트를 사용하면 단일 Azure 테넌트에 여러 개의 고유한 관리되는 도메인을 배포할 수 없습니다. 각 복제본 세트에는 동일한 데이터가 포함되어 있습니다.

## <a name="how-replica-sets-work"></a>복제본 세트의 작동 방식

*aaddscontoso.com* 같은 관리되는 도메인을 만들 때 초기 복제본 세트가 만들어집니다. 추가 복제본 세트는 동일한 네임스페이스와 구성을 공유합니다. 구성, 사용자 ID 및 자격 증명, 그룹, 그룹 정책 개체, 컴퓨터 개체를 비롯한 Azure AD DS 변경 내용과 기타 변경 내용은 AD DS 복제를 사용하는 관리되는 도메인의 모든 복제본 세트에 적용됩니다.

가상 네트워크에서 각 복제본 세트를 만듭니다. 각 가상 네트워크는 관리되는 도메인의 복제본 세트를 호스팅하는 다른 모든 가상 네트워크에 피어링해야 합니다. 이 구성은 디렉터리 복제를 지 원하는 메시 네트워크 토폴로지를 만듭니다. 각 복제본 세트가 다른 가상 서브넷에 있는 경우, 가상 네트워크는 여러 개의 복제본 세트를 지원할 수 있습니다.

모든 복제본 세트는 동일한 Active Directory 사이트에 배치됩니다. 그 결과에 따라 모든 변경 내용은 빠른 수렴을 위해 사이트 간 복제를 사용하여 전파됩니다.

> [!NOTE]
> 별도의 사이트를 정의하거나, 복제본 세트 간에 복제 설정을 정의하는 것은 가능하지 않습니다.

다음 다이어그램에서는 두 개의 복제본 세트를 가진 관리되는 도메인을 보여 줍니다. 첫 번째 복제본 세트는 도메인 네임스페이스를 사용하여 생성됩니다. 두 번째 복제본 세트는 그다음에 생성됩니다.

![두 개의 복제본 세트를 가진 관리되는 도메인 예제의 다이어그램](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> 복제본 세트는 복제본 세트가 구성된 지역에서 인증 서비스의 가용성을 보장합니다. 지리적 중복성이 있는 애플리케이션의 경우, 지역 가동 중단이 있다면 관리되는 도메인에 의존하는 애플리케이션 플랫폼도 다른 지역에 상주해야 합니다.
>
> 애플리케이션을 작동하는 데 필요한 다른 서비스(예: Azure VM 또는 Azure App Service)의 복원력은 복제본 세트에서 제공하지 않습니다. 다른 애플리케이션 구성 요소의 가용성 디자인은 해당 애플리케이션을 구성하는 서비스에 대한 복원력 기능을 고려해야 합니다.

다음 예제에서는 복원력을 추가로 제공하고 인증 서비스의 가용성을 보장하기 위해 세 개의 복제본 세트를 가진 관리되는 도메인을 보여 줍니다. 두 예제에서 애플리케이션 워크로드는 관리되는 도메인 복제본 세트와 동일한 지역에 있습니다.

![세 개의 복제본 세트를 가진 관리되는 도메인 예제의 다이어그램](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>배포 고려 사항

관리되는 도메인의 기본 SKU는 여러 복제본 세트를 지원하는 *Enterprise* SKU입니다. *표준* SKU로 변경한 경우 추가 복제본 세트를 만들려면, [관리되는 도메인](change-sku.md)을 *Enterprise* 또는 *프리미엄* 으로 업그레이드합니다.

지원되는 최대 복제본 세트 수는 5 개입니다. 여기에는 관리되는 도메인을 만들 때 생성되는 첫 번째 복제본이 포함됩니다.

각 복제본 세트에 대한 청구는 도메인 구성 SKU를 기반으로 합니다. 예를 들어, *Enterprise* SKU를 사용하는 관리되는 도메인이 있고 세 개의 복제본 세트가 있는 경우, 세 개의 복제본 세트 각각에 대해 구독이 시간당 청구됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>구독에서 관리되는 도메인과 다른 복제본 세트를 만들 수 있나요?

아니요. 복제본 세트는 관리되는 도메인과 동일한 구독에 있어야 합니다.

### <a name="how-many-replica-sets-can-i-create"></a>얼마나 많은 복제본 세트를 생성할 수 있습니까?

최대 5개의 복제본 세트(관리되는 도메인에 대한 초기 복제본 세트 및 4개의 추가 복제본 세트)를 만들 수 있습니다.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>사용자 및 그룹 정보가 내 복제본 세트에 동기화되는 방법

모든 복제본 세트는 메시 가상 네트워크 피어링을 사용하여 서로 연결됩니다. 하나의 복제본 세트가 Azure AD에서 사용자 및 그룹 업데이트를 받습니다. 그다음 이러한 변경 내용은 피어링된 네트워크를 통해 사이트 간 AD DS 복제를 사용하여 다른 복제본 세트에 복제됩니다.

온-프레미스 AD DS와 마찬가지로 확장되고 연결이 끊어진 상태를 사용하면 복제가 중단될 수 있습니다. 피어링된 가상 네트워크는 전이적이지 않으므로, 복제본 세트에 대한 디자인 요구 사항에는 완전하게 메쉬된 네트워크 토폴로지가 필요합니다.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>복제본 세트가 있는 경우 관리되는 도메인에서 변경을 어떻게 만드나요?

관리되는 도메인 내의 변경 내용은 이전에 수행한 것과 동일한 방식으로 작동합니다. [관리되는 도메인에 조인된 RSAT 도구를 사용하여 관리 VM을 만들고 사용합니다](tutorial-create-management-vm.md). 관리되는 도메인에 원하는 수만큼의 관리 VM을 조인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

복제본 세트를 시작하려면 [Azure AD DS 관리되는 도메인을 만들고 구성합니다][tutorial-create-advanced]. 배포된 경우, [추가 복제본 세트를 만들고 사용합니다][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
