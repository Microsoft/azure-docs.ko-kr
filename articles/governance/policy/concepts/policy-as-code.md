---
title: 코드 제공 정책 워크플로 설계
description: Azure Policy 정의를 코드로 배포하고 리소스의 유효성을 자동으로 검사하는 워크플로를 설계하는 방법을 알아봅니다.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: d46680a9978cd4ec5cdc612a709f031841716749
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047329"
---
# <a name="design-policy-as-code-workflows"></a>코드 제공 정책 워크플로 설계

Cloud Governance 여정을 진행하면서 Azure Portal이나 다양한 SDK를 통해 각 정책 정의를 수동으로 관리하는 방식을 엔터프라이즈 규모에서 보다 관리하기 쉽고 반복 가능한 방식으로 전환하려고 합니다. 클라우드에서 대규모로 시스템을 관리하는 일반적인 방법 두 가지는 다음과 같습니다.

- 코드로 서의 인프라: 환경을 정의 하는 콘텐츠를 처리 하는 방법입니다. Azure Resource Manager 템플릿 (ARM 템플릿)에서 Azure 청사진에 대 한 정의를 소스 코드로 Azure Policy 하는 것입니다.
- DevOps: 최종 사용자에게 가치를 지속적으로 제공할 수 있도록 하는 사람, 프로세스 및 제품을 통칭

이러한 아이디어의 조합이 바로 코드 제공 정책입니다. 기본적으로 정책 정의를 소스 제어에서 유지하고, 변경 내용이 적용될 때마다 해당 변경 내용을 테스트하고, 유효성을 검사합니다. 그러나 이는 코드 제공 인프라 또는 DevOps와 관련된 정책 범위에 있어서는 안 됩니다.

또한 유효성 검사 단계는 다른 연속 통합 또는 지속적인 배포 워크플로의 구성 요소여야 합니다. 예를 들어 애플리케이션 환경 또는 가상 인프라 배포 등이 있습니다. 응용 프로그램 및 운영 팀은 빌드 및 배포 프로세스의 초기 구성 요소에 대 한 Azure Policy 유효성 검사를 수행 하 여 변경 내용이 비규격 인지 여부를 확인 합니다.

## <a name="definitions-and-foundational-information"></a>정의 및 기본 정보

정책 세부 정보를 코드 워크플로로 가져오기 전에 다음 정의 및 예제를 검토 합니다.

- [정책 정의](./definition-structure.md)
- [이니셔티브 정의](./initiative-definition-structure.md)

파일 이름은 정책 또는 이니셔티브 정의의 일부에 맞게 정렬 됩니다.
- `policy(set).json` -전체 정의
- `policy(set).parameters.json` - `properties.parameters` 정의의 일부입니다.
- `policy.rules.json` - `properties.policyRule` 정의의 일부입니다.
- `policyset.definitions.json` - `properties.policyDefinitions` 정의의 일부입니다.

이러한 파일 형식의 예는 [Azure Policy GitHub](https://github.com/Azure/azure-policy/)리포지토리에서 사용할 수 있습니다.

- 정책 정의: [리소스에 태그 추가](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- 이니셔티브 정의: [청구 태그](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>워크플로 개요

코드 제공 정책에 권장되는 일반적인 워크플로는 다음 다이어그램과 같습니다.

:::image type="content" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="코드 제공 정책 워크플로 개요" border="false":::

### <a name="create-and-update-policy-definitions"></a>정책 정의 만들기 및 관리

정책 정의는 JSON을 사용하여 생성되고 소스 제어에 저장됩니다. 각 정책에는 동일한 폴더에 저장해야 하는 매개 변수, 규칙 및 환경 매개 변수와 같은 고유한 파일 세트가 있습니다. 다음 구조는 소스 제어에서 정책 정의를 유지할 때 권장되는 방법입니다.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

새 정책이 추가되거나 기존 정책이 업데이트되면 워크플로가 Azure의 정책 정의를 자동으로 업데이트해야 합니다. 신규 또는 업데이트된 정책 정의 테스트는 이후 단계에서 수행합니다.

### <a name="create-and-update-initiative-definitions"></a>이니셔티브 정의 만들기 및 업데이트

이니셔티브에도 정책 정의와 마찬가지로 동일한 폴더에 저장해야 하는 고유한 JSON 파일 및 관련 파일이 있습니다. 이니셔티브 정의를 사용하려면 정책 정의가 이미 있어야 하므로 정책 소스가 소스 제어에서 업데이트된 후 Azure에서 업데이트되어야만 이니셔티브 정의를 만들거나 업데이트할 수 있습니다. 다음 구조는 소스 제어에서 이니셔티브 정의를 유지할 때 권장되는 방법입니다.

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

정책 정의와 마찬가지로 기존 이니셔티브를 추가 하거나 업데이트할 때 워크플로는 Azure에서 이니셔티브 정의를 자동으로 업데이트해야 합니다. 신규 또는 업데이트된 이니셔티브 정의 테스트는 이후 단계에서 수행합니다.

### <a name="test-and-validate-the-updated-definition"></a>업데이트된 정의 테스트 및 유효성 검사

Automation에서 새로 생성되거나 업데이트된 정책 또는 이니셔티브 정의를 가져와 Azure의 개체를 업데이트한 후에는 변경 내용을 테스트해야 합니다. 정책 또는 관련 이니셔티브는 프로덕션 단계보다 훨씬 이전에 리소스에 할당되어야 합니다. 이 환경은 일반적으로 _Dev_입니다.

할당은 리소스 생성과 업데이트가 차단되지 않도록 _사용 안 함_ [enforcementMode](./assignment-structure.md#enforcement-mode)를 사용해야 하지만 기존 리소스는 업데이트된 정책 정의 준수 여부를 계속 감사합니다. enforcementMode를 사용하는 경우에도 할당 범위는 정책 유효성 검사 전용 리소스 그룹 또는 구독이어야 합니다.

> [!NOTE]
> 적용 모드는 유용하지만 다양한 조건에서 정책 정의를 철저히 테스트하지 않고 사용해서는 안 됩니다. 정책 정의는 `PUT` 및 `PATCH` REST API 호출, 준수 및 미준수 리소스, 리소스에서 누락된 속성과 같은 에 지 사례를 사용하여 테스트해야 합니다.

할당을 배포한 후 정책 SDK 또는 [Azure Policy 준수 검색 GitHub 작업](https://github.com/marketplace/actions/azure-policy-compliance-scan) 을 사용 하 여 새 할당에 대 한 [호환성 데이터를 가져옵니다](../how-to/get-compliance-data.md) . 정책 및 할당을 테스트하는 데 사용되는 환경에는 준수 및 미준수 리소스가 둘 다 있어야 합니다.
코드에 대한 유용한 단위 테스트와 마찬가지로, 리소스가 예상대로 작동하고 가양성 또는 가음성이 없는지도 테스트하려고 합니다. 예상되는 항목에 대해서만 테스트 및 유효성 검사를 수행하는 경우 미처 파악하지 못한 예기치 않은 정책 영향이 있을 수 있습니다. 자세한 내용은 [새 Azure Policy 정의의 영향 평가](./evaluate-impact.md)를 참조하세요.

### <a name="enable-remediation-tasks"></a>수정 작업 사용

할당의 유효성 검사가 기대치를 충족하는 경우 다음 단계는 수정의 유효성을 검사하는 것입니다.
[deployIfNotExists](./effects.md#deployifnotexists) 또는 [modify](./effects.md#modify)를 사용하는 정책은 수정 작업으로 전환되어 리소스의 미준수 상태를 해결할 수 있습니다.

리소스를 수정하는 첫 번째 단계는 정책 정의에 기술된 역할 할당에 정책 할당을 부여하는 것입니다. 이 역할 할당은 정책 할당 관리 ID에 리소스를 준수하는 데 필요한 변경을 수행할 수 있는 충분한 권한을 제공합니다.

정책 할당에 적절한 권한이 있는 경우 Policy SDK를 사용하여 미준수 상태로 알려진 리소스 세트에 대한 수정 작업을 트리거하세요. 계속하기 전에 이처럼 수정된 작업에 대한 세 가지 테스트를 완료해야 합니다.

- 수정 작업이 성공적으로 완료되었는지 확인
- 정책 평가를 실행하여 정책 준수 결과가 예상대로 업데이트되는지 확인
- 리소스에 대한 환경 단위 테스트를 직접 실행하여 해당 속성이 변경되었는지 확인

업데이트된 정책 평가 결과와 환경을 모두 테스트하면 수정 작업이 올바르게 변경 내용을 적용했으며 정책 정의가 준수 상태 변경을 올바르게 인식했는지 직접 확인할 수 있습니다.

### <a name="update-to-enforced-assignments"></a>적용된 할당으로 업데이트

모든 유효성 검사 게이트가 완료된 후 _enabled_라는 **enforcementMode**를 사용하도록 할당을 업데이트합니다. 프로덕션 단계보다 훨씬 이전에 동일한 환경에서 이 변경을 수행하는 것이 좋습니다. 올바르게 작동하는 환경의 유효성 검사를 완료한 후에는 정책이 프로덕션 리소스에 배포될 때까지 다음 환경을 포함하도록 변경 범위를 지정해야 합니다.

## <a name="process-integrated-evaluations"></a>프로세스 통합 평가

일반적인 코드 제공 정책 워크플로는 정책 및 이니셔티브를 대규모로 개발하고 환경에 배포하기 위한 것입니다. 그러나 정책 평가는 인프라를 만들기 위해 응용 프로그램 배포 또는 ARM 템플릿 실행과 같이 Azure에서 리소스를 배포 하거나 만드는 모든 워크플로에 대 한 배포 프로세스의 일부 여야 합니다.

이러한 경우 테스트 구독 또는 리소스 그룹에 대해 애플리케이션 또는 인프라 배포가 수행된 후 해당 범위에 대한 정책 평가를 수행하여 모든 기존 정책과 이니셔티브의 유효성을 확인해야 합니다. 이러한 환경에서는 **enforcementMode**가 _사용 안 함_으로 구성될 수 있지만 애플리케이션 또는 인프라 배포가 정책 정의를 위반하는지 초기에 확인하기에 유용합니다. 따라서 이 정책 평가는 이러한 워크플로의 한 단계로 포함되어야 하며 미준수 리소스를 만드는 배포에 실패합니다.

## <a name="review"></a>검토

이 문서에서는 일반적인 코드 제공 정책 워크플로를 설명하고, 정책 평가가 다른 배포 워크플로에 포함되어야 하는 경우도 설명합니다. 이 워크플로는 트리거를 기반으로 하는 스크립팅된 단계 및 자동화를 지원하는 모든 환경에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [정책 할당 구조](./assignment-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 생성](../how-to/programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
