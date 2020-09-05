---
title: Azure Blueprints 개요
description: Azure Blueprints 서비스를 통해 Azure 환경에서 아티팩트를 만들고 정의하고 배포하는 방법을 알아봅니다.
ms.date: 08/27/2020
ms.topic: overview
ms.openlocfilehash: e5c08f4211f03ddc6d2f48eee4fc84a824732e43
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050780"
---
# <a name="what-is-azure-blueprints"></a>Azure Blueprints란?

엔지니어나 설계자가 청사진을 통해 프로젝트의 설계 매개 변수를 스케치하는 것과 마찬가지로, 클라우드 설계자와 중앙 정보 기술 그룹은 Azure Blueprints를 통해 조직의 표준, 패턴 및 요구 사항을 구현하고 준수하는 반복 가능한 Azure 리소스 집합을 정의할 수 있습니다. Azure Blueprints를 사용하면 개발 팀에서 네트워킹과 같은 기본 제공 구성 요소 세트를 사용하여 조직의 규정을 준수하는 신뢰할 수 있는 새 환경을 빠르게 빌드하고 구축함으로써 개발 및 배포 속도를 높일 수 있습니다.

즉, Blueprints에서는 다음과 같은 다양한 리소스 템플릿 및 기타 아티팩트의 배포를 명확하게 조정할 수 있습니다.

- 역할 할당
- 정책 할당
- ARM 템플릿(Azure Resource Manager 템플릿)
- 리소스 그룹

Azure Blueprints 서비스는 전역적으로 분산된 [Azure Cosmos DB](../../cosmos-db/introduction.md)를 통해 백업됩니다. 청사진 개체는 여러 Azure 지역에 복제됩니다. 이렇게 복제하면 Azure Blueprints가 리소스를 배포하는 Azure 지역에 관계없이 대기 시간이 짧아지고, 가용성이 향상되고, 청사진 개체에 일관적으로 액세스할 수 있습니다.

## <a name="how-its-different-from-arm-templates"></a>ARM 템플릿과의 차이점

이 서비스는 _환경 설정_에 도움이 되도록 설계되었습니다. 이 설정은 리소스 그룹, 정책, 역할 할당 및 ARM 템플릿 배포 세트로 구성되는 경우가 많습니다. 청사진은 이러한 각 _아티팩트_ 형식을 모두 결합하는 패키지이며, CI/CD 파이프라인 사용을 포함하여 해당 패키지를 구성하고 버전을 지정할 수 있습니다. 최종적으로 각 청사진은 감사 및 추적이 가능한 한 번의 작업을 통해 구독에 할당됩니다.

Azure Blueprints에 배포하기 위해 포함하려는 거의 모든 항목은 ARM 템플릿으로 수행할 수 있습니다. 하지만 ARM 템플릿은 Azure에서 기본적으로 제공되지 않는 문서이며 로컬이나 소스 제어에 저장됩니다. 템플릿은 Azure 리소스 하나 이상의 배포에 사용되기는 하지만, 해당 리소스가 배포되고 나면 해당 템플릿에 대한 활성 연결과 관계는 손실됩니다.

반면 Azure Blueprints를 사용하는 경우에는 청사진 정의(_배포해야 하는 항목_)와 청사진 할당(_배포된 항목_) 간의 관계가 유지됩니다. 이 연결은 배포에 대한 향상된 추적 및 감사를 지원합니다. Azure Blueprints는 동일한 청사진에서 관리하는 여러 구독을 한 번에 업그레이드할 수도 있습니다.

ARM 템플릿과 청사진 중 하나만 선택할 필요는 없습니다. 각 청사진은 ARM 템플릿 _아티팩트_를 포함하지 않을 수도 있고 하나 이상 포함할 수도 있습니다. 즉, 이전에 개발 및 유지 관리해 왔던 ARM 템플릿 라이브러리를 Azure Blueprints에서 재사용할 수 있습니다.

## <a name="how-its-different-from-azure-policy"></a>Azure Policy와 Blueprints의 차이점

청사진은 일관성과 규정 준수 상태를 유지하기 위해 다시 사용할 수 있는 Azure Cloud Services/보안/디자인 구현 관련 표준/패턴/요구 사항의 포커스별 집합으로 구성된 패키지 또는 컨테이너입니다.

반면 [정책](../policy/overview.md)은 배포 중에, 그리고 기존 리소스에 대해 적용되는 리소스 속성 중심의 기본 허용 및 명시적 거부 시스템입니다. 정책은 구독 내의 리소스가 요구 사항과 표준을 준수하는지를 확인하여 클라우드 거버넌스를 지원합니다.

청사진에 정책을 포함하면 청사진 할당 중에 올바른 패턴 또는 디자인을 만들 수 있습니다. 정책을 포함하면 해당 환경에 승인되거나 예상된 변경 사항만 적용되어 청사진의 본래 의도를 지속적으로 지킬 수 있습니다.

청사진 정의의 여러 _아티팩트_ 중 하나로 정책을 포함할 수 있습니다. Blueprints에서는 정책과 이니셔티브에 매개 변수를 사용할 수도 있습니다.

## <a name="blueprint-definition"></a>청사진 정의

청사진은 _아티팩트_로 구성됩니다. 현재 Azure Blueprints에서 아티팩트로 지원하는 리소스는 다음과 같습니다.

|리소스  | 계층 구조 옵션| Description  |
|---------|---------|---------|
|리소스 그룹 | Subscription | 청사진 내의 다른 아티팩트에 사용할 새 리소스 그룹을 만듭니다.  이러한 자리 표시자 리소스 그룹을 사용하면 정확히 원하는 구조로 리소스를 구성할 수 있으며, 포함된 정책 및 역할 할당 아티팩트와 ARM 템플릿의 범위도 제한할 수 있습니다. |
|ARM 템플릿 | 구독, 리소스 그룹 | 중첩된 템플릿과 연결된 템플릿을 포함하는 템플릿은 복잡한 환경을 구성하는 데 사용됩니다. SharePoint 팜, Azure Automation State Configuration 또는 Log Analytics 작업 영역과 같은 환경을 예로 들 수 있습니다. |
|정책 할당 | 구독, 리소스 그룹 | 청사진이 할당된 구독에 정책 또는 이니셔티브를 할당할 수 있습니다. 정책이나 이니셔티브는 청사진 정의 위치 범위 내에 있어야 합니다. 정책 또는 이니셔티브에 매개 변수가 있는 경우 청사진 생성 시나 할당 중에 이러한 매개 변수를 할당할 수 있습니다. |
|역할 할당 | 구독, 리소스 그룹 | 적절한 사용자가 리소스에 대한 적절한 액세스 권한을 항상 소유하도록 기존 사용자나 그룹을 기본 제공 역할에 추가합니다. 역할 할당은 전체 구독에 대해 정의할 수도 있고 청사진에 포함된 특정 리소스 그룹에 중첩할 수도 있습니다. |

### <a name="blueprint-definition-locations"></a>청사진 정의 위치

청사진 정의를 만들 때는 청사진을 저장할 위치를 정의합니다. 청사진은 **기여자** 권한이 있는 [관리 그룹](../management-groups/overview.md) 또는 구독에 저장할 수 있습니다. 위치가 관리 그룹인 경우 청사진은 해당 관리 그룹의 모든 하위 구독에 할당할 수 있습니다.

### <a name="blueprint-parameters"></a>청사진 매개 변수

Blueprints에서는 정책/이니셔티브 또는 ARM 템플릿에 매개 변수를 전달할 수 있습니다. 이 두 _아티팩트_ 중 하나를 청사진에 추가하면 다른 아티팩트가 각 청사진 할당에 대해 정의된 값을 제공할지, 아니면 각 청사진 할당이 할당 시에 값을 제공하도록 허용할지를 결정합니다. 이처럼 유동적인 방식이 사용되므로 청사진을 사용할 때마다 미리 정의된 값을 정의할 수도 있고, 할당 시에 해당 값을 결정하도록 설정할 수도 있습니다.

> [!NOTE]
> 청사진은 자체 매개 변수를 포함할 수 있지만 현재는 Portal이 아닌 REST API에서 청사진을 생성하는 경우에만 이러한 매개 변수를 만들 수 있습니다.

자세한 내용은 [청사진 매개 변수](./concepts/parameters.md)를 참조하세요.

### <a name="blueprint-publishing"></a>청사진 게시

처음 만든 청사진은 **초안** 모드로 간주됩니다. 할당할 준비가 된 청사진은 **게시**해야 합니다. 청사진을 게시하려면 **버전** 문자열(문자/숫자/하이픈 사용, 최대 20자)과 선택적 **변경 메모**를 정의해야 합니다. **버전**을 정의하면 해당 청사진을 같은 청사진의 이후 변경 버전과 구별할 수 있으며 각 버전을 할당할 수 있습니다. 또한 같은 청사진의 여러 **버전**을 같은 구독에 할당할 수도 있습니다. 청사진을 추가로 변경해도 **게시된**
**버전**은 그대로 있으며 **게시되지 않은 변경 내용**도 생성됩니다. 변경이 완료되면 업데이트된 청사진이 새로운 고유 **버전**으로 **게시**되므로 역시 할당 가능합니다.

## <a name="blueprint-assignment"></a>청사진 할당

청사진의 각 **게시된** **버전**을 기존 구독에 할당할 수 있습니다(최대 이름 길이는 90자). 포털에서 청사진의 기본 **버전**은 가장 최근에 **게시된** 버전입니다. 아티팩트 매개 변수나 청사진 매개 변수가 있으면 할당 프로세스 중에 매개 변수가 정의됩니다.

## <a name="permissions-in-azure-blueprints"></a>Azure Blueprints의 권한

청사진을 사용하려면 RBAC([역할 기반 액세스 제어](../../role-based-access-control/overview.md))를 통해 권한을 부여받아야 합니다. 청사진을 만들려면 계정에 다음과 같은 권한이 필요합니다.

- `Microsoft.Blueprint/blueprints/write` - 청사진 정의 만들기
- `Microsoft.Blueprint/blueprints/artifacts/write` - 청사진 정의에 아티팩트 만들기
- `Microsoft.Blueprint/blueprints/versions/write` - 청사진 게시

청사진을 삭제하려면 계정에 다음과 같은 권한이 필요 합니다.

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> 청사진 정의 권한은 청사진이 저장된 관리 그룹 또는 구독 범위에 부여되거나 상속되어야 합니다.

청사진을 할당하거나 할당을 해제하려면 계정에 다음과 같은 권한이 필요합니다.

- `Microsoft.Blueprint/blueprintAssignments/write` - 청사진 할당
- `Microsoft.Blueprint/blueprintAssignments/delete` - 청사진 할당 해제

> [!NOTE]
> 청사진 할당은 구독에서 생성되므로 청사진 할당 및 할당 해제 권한은 구독 범위에서 부여되거나 구독 범위로 상속되어야 합니다.

다음과 같은 기본 제공 역할을 사용할 수 있습니다.

|Azure 역할 | Description |
|-|-|
|[소유자](../../role-based-access-control/built-in-roles.md#owner) | 다른 권한 외에도 모든 Azure Blueprint 관련 권한이 포함됩니다. |
|[기여자](../../role-based-access-control/built-in-roles.md#contributor) | 다른 권한 외에도 청사진 정의를 만들고 삭제할 수 있지만 청사진 할당 권한은 없습니다. |
|[청사진 기여자](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | 청사진 정의를 관리할 수 있지만 할당할 수는 없습니다. |
|[청사진 연산자](../../role-based-access-control/built-in-roles.md#blueprint-operator) | 게시된 기존 청사진을 할당할 수 있지만 새 청사진 정의를 만들 수는 없습니다. 청사진 할당은 할당이 사용자 할당 관리 ID로 수행되는 경우에만 작동합니다. |

이러한 기본 제공 역할이 보안 요구에 적합하지 않다면 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 만드는 것이 좋습니다.

> [!NOTE]
> 시스템 할당 관리 ID를 사용하는 경우 Azure Blueprints의 서비스 주체가 배포를 사용하려면 할당된 구독에 대한 **소유자** 역할이 필요합니다. 포털을 사용하는 경우에는 이 역할이 배포에 대해 자동으로 부여 및 취소됩니다. REST API를 사용하는 경우에는 이 역할을 수동으로 부여해야 합니다. 하지만 배포가 완료되면 역할은 자동으로 취소됩니다. 사용자가 할당한 관리 ID를 사용하는 경우 청사진 할당을 만드는 사용자에게만 **소유자** 및 **청사진 운영자** 기본 제공 역할 둘 다에 포함되는 `Microsoft.Blueprint/blueprintAssignments/write` 권한이 필요합니다.

## <a name="naming-limits"></a>이름 지정 제한

특정 필드에 다음과 같은 제한 사항이 존재합니다.

|Object|필드|허용되는 문자|최대 길이|
|-|-|-|-|
|청사진|Name|문자, 숫자, 하이픈 및 마침표|48|
|청사진|버전|문자, 숫자, 하이픈 및 마침표|20|
|청사진 할당|Name|문자, 숫자, 하이픈 및 마침표|90|
|청사진 아티팩트|Name|문자, 숫자, 하이픈 및 마침표|48|

## <a name="video-overview"></a>비디오 개요

Azure Blueprints에 대한 다음 개요는 Azure Friday에서 가져온 것입니다. 비디오를 다운로드하려면 Channel 9의 [Azure Fridays - Azure Blueprints 개요](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints)를 방문하세요.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>다음 단계

- [청사진 만들기 - Portal](./create-blueprint-portal.md).
- [청사진 만들기 - PowerShell](./create-blueprint-powershell.md).
- [청사진 만들기 - REST API](./create-blueprint-rest-api.md).