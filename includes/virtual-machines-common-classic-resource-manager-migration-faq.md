---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: 74496cd3d4cd01be326baae870b075eb923983af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581146"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>클래식에서 Azure Resource Manager 마이그레이션에 대한 질문과 대답

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>이 마이그레이션 계획이 Azure 가상 머신에서 실행되는 기존 서비스 또는 애플리케이션에 영향을 미치나요? 

아니요. VM(클래식)은 일반 공급 제품에서 완전하게 지원되는 서비스입니다. 이러한 리소스를 사용하여 Microsoft Azure에서 작업 공간을 확장할 수 있습니다.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>가까운 미래에 마이그레이션할 계획이 없는 경우 내 VM은 어떻게 됩니까? 

Microsoft는 기존 클래식 API와 리소스 모델을 중단할 계획이 없으며 보다 편리한 마이그레이션을 위해 Resource Manager 배포 모델에서 사용할 수 있는 고급 기능을 고려하고 있습니다. Resource Manager의 IaaS로 구현된 [몇 가지 개선 사항](../articles/azure-resource-manager/resource-manager-deployment-model.md)을 살펴볼 것을 권장합니다.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>이 마이그레이션 계획으로 기존 도구는 어떻게 되나요? 

마이그레이션 계획에서 가장 중요하게 수행해야 할 변경 사항 중 하나는 사용자 도구를 Resource Manager 배포 모델에 맞게 업데이트하는 것입니다.

## <a name="how-long-will-the-management-plane-downtime-be"></a>관리 평면 가동 중지 시간은 얼마나 되나요? 

마이그레이션하는 리소스의 수에 따라 달라집니다. 소규모 배포(몇 십 대의 VM)의 경우 전체 마이그레이션은 1시간 미만이 소요됩니다. 대규모 배포(수백 대의 VM)의 경우 마이그레이션에 몇 시간이 걸릴 수 있습니다.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Resource Manager에서 마이그레이션 리소스를 커밋한 다음 롤백할 수 있나요? 

리소스가 준비됨 상태인 경우에만 마이그레이션을 중단할 수 있습니다. 커밋 작업을 사용하여 리소스를 성공적으로 마이그레이션한 후에는 롤백이 지원되지 않습니다.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>커밋 작업이 실패한 경우 마이그레이션을 롤백할 수 있나요? 

커밋 작업이 실패한 경우 마이그레이션을 중단할 수 없습니다. 커밋 작업을 포함한 모든 마이그레이션 작업은 idempotent 상태입니다. 따라서 짧은 기간 이후 작업을 다시 시도해보는 것이 좋습니다. 그래도 오류가 발생할 경우 지원 티켓을 만들거나 [VM 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)에서 ClassicIaaSMigration 태그로 포럼 게시물을 작성할 수 있습니다.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Resource Manager에서 IaaS를 활용해야 할 경우 다른 Express 경로 회로를 구매해야 하나요? 

아니요. 최근에 [클래식에서 Resource Manager 배포 모델로의 ExpressRoute 회로 이동](../articles/expressroute/expressroute-move.md)을 사용하도록 설정했습니다. 이미 ExpressRoute 회로가 있는 경우 새로 구입하지 않아도 됩니다.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>클래식 IaaS 리소스에 대해 역할 기반 Access Control 정책을 구성한 경우 어떻게 되나요? 

마이그레이션 중에는 리소스가 클래식에서 Resource Manager로 전환됩니다. 따라서 마이그레이션 후 필요한 RBAC 정책 업데이트를 계획하는 것이 좋습니다.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>자격 증명 모음에 내 클래식 VM을 백업했습니다. 클래식 모드에서 Resource Manager 모드로 내 VM을 마이그레이션하고 Recovery Services 자격 증명 모음에서 보호할 수 있나요?

클래식 모드에서 Resource Manager 모드로 VM을 이동하는 <a name="vault">경우</a> 마이그레이션 이전에 생성된 백업은 새로 마이그레이션된 Resource Manager VM으로 마이그레이션되지 않습니다. 하지만 클래식 VM의 백업을 보관하려는 경우 마이그레이션 전에 다음 단계를 수행하세요. 

1. Recovery Services 자격 증명 모음에서 **보호된 항목** 탭으로 이동하고 VM을 선택합니다. 
2. 보호 중지를 클릭합니다. *연결된 백업 데이터 삭제* 옵션을 **검사하지 않음**으로 둡니다.

> [!NOTE]
> 데이터를 보존할 때까지 백업 인스턴스 비용이 청구됩니다. 백업 복사본은 보존 범위에 따라 정리됩니다. 그러나 마지막 백업 복사본은 백업 데이터를 명시적으로 삭제할 때까지 항상 유지됩니다. 가상 머신의 보존 범위를 확인하고, 이러한 보존 범위가 경과한 경우 자격 증명 모음에서 보호된 항목에 대해 "백업 데이터 삭제"를 트리거하는 것이 좋습니다. 
>
>

가상 머신을 Resource Manager 모드로 마이그레이션하려면 

1. VM에서 백업/스냅숏 확장을 삭제합니다.
2. 클래식 모드에서 Resource Manager 모드로 가상 머신을 마이그레이션합니다. 가상 머신에 해당하는 저장소 및 네트워크 정보가 Resource Manager 모드로 마이그레이션되도록 합니다.

또한 마이그레이션된 VM을 백업하려는 경우 가상 머신 관리 블레이드로 이동하여 [백업을 사용하도록 설정](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)합니다.

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>내 구독 또는 리소스에서 마이그레이션이 가능한지 확인할 수 있나요? 

예. 플랫폼 지원 마이그레이션 옵션에서 마이그레이션을 준비하는 첫 번째 단계는 리소스를 마이그레이션할 수 있는지 여부를 확인하는 것입니다. 유효성 검사 작업이 실패하는 경우, 마이그레이션을 완료할 수 없는 모든 원인에 대한 모든 메시지가 나타납니다.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>마이그레이션을 위해 IaaS 리소스를 준비하는 동안 할당량 오류가 발생하면 어떻게 되나요? 

마이그레이션을 중단한 다음 지원 요청을 로깅하여 VM을 마이그레이션하고 있는 지역에서 할당량을 늘리는 것이 좋습니다. 할당량 요청이 승인되면 마이그레이션 단계를 다시 실행할 수 있습니다.

## <a name="how-do-i-report-an-issue"></a>문제를 보고하려면 어떻게 해야 하나요? 

키워드 ClassicIaaSMigration을 사용하여 [VM 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)에 마이그레이션에 대한 문제와 질문을 게시하세요. 이 포럼에 모든 질문을 게시하는 것이 좋습니다. 지원 계약을 맺은 경우 지원 티켓을 로깅해도 좋습니다.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>마이그레이션 중 플랫폼이 선택한 리소스 이름이 마음에 들지 않으면 어떻게 하나요? 

클래식 배포 모델에서 명시적으로 이름을 입력한 모든 리소스는 마이그레이션 중 해당 이름이 보존됩니다. 새 리소스가 생성되는 경우도 있습니다. 예를 들어 모든 VM에 대해 네트워크 인터페이스가 생성됩니다. 현재는 마이그레이션 중 이와 같이 새로 생성된 리소스 이름을 제어하는 기능을 지원하지 않습니다. [Azure 사용자 의견 포럼](http://feedback.azure.com)에서 이 기능에 대한 투표를 실시하세요.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>권한 부여 링크를 통해 구독에서 사용되는 ExpressRoute 회로를 마이그레이션할 수 있습니까? 

구독 간 권한 부여 링크를 사용하는 ExpressRoute 회로는 가동 중지 시간 없이 자동으로 마이그레이션할 수 없습니다. 여기에서는 수동 단계를 사용하여 마이그레이션하는 방법을 소개합니다. 해당 단계와 자세한 내용은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션](../articles/expressroute/expressroute-migration-classic-resource-manager.md)을 참조하세요.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>*"VM은 전반적인 에이전트 상태를 준비되지 않음으로 보고합니다. 따라서 VM은 마이그레이션할 수 없습니다. VM 에이전트가 전반적인 에이전트 상태를 준비된 상태"* 로 보고하고 있는지 또는 *"VM에서 보고되지 않은 상태의 확장이 VM에 포함되어 있는지 확인합니다. 따라서 이 VM은 마이그레이션할 수 없습니다."* 라는 메시지를 받았습니다.

VM이 인터넷에 아웃바운드 연결하지 못하는 경우 이 메시지가 수신됩니다. VM 에이전트는 아웃 바운드 연결을 사용하여 Azure Storage 계정에 연결해 5분 마다 에이전트 상태를 업데이트합니다.
