---
title: 지침 및 모범 사례
description: 클라우드 및 온-프레미스 워크 로드를 클라우드로 백업 하기 위한 모범 사례 및 지침을 알아봅니다.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: db6eec5351a9015b136226610d2bb3deb8bdc651
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000365"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>클라우드 및 온-프레미스 워크 로드를 클라우드로 백업

## <a name="introduction"></a>소개

Azure Backup 인프라를 사용 하지 않는 간단 하 고 안전 하며 비용 효율적인 솔루션을 통해 Azure에서 데이터 자산을 포괄적으로 보호 합니다. 다양 한 워크 로드에 대 한 Azure's 기본 제공 데이터 보호 솔루션입니다. 클라우드에서 실행 되는 중요 업무용 워크 로드를 보호 하 고 백업을 항상 사용할 수 있으며 전체 백업 공간에서 대규모로 관리할 수 있도록 합니다.

### <a name="intended-audience"></a>대상 그룹

이 문서의 주 대상 사용자는 IT 및 응용 프로그램 관리자, Azure의 기본 제공 데이터 보호 Azure Backup 기술 기능에 대해 배우고 배포를 더 잘 보호 하는 솔루션을 효율적으로 구현 하는 방법에 대 한 정보를 제공 하는 대규모의 조직 및 응용 프로그램 관리자를 위한 구현자입니다. 이 문서에서는 핵심 Azure 기술, 데이터 보호 개념 및 백업 솔루션을 사용 하는 경험을 잘 알고 있다고 가정 합니다. 이 문서에서 설명 하는 지침을 통해 설정 된 패턴을 사용 하 여 Azure에서 백업 솔루션을 보다 쉽게 디자인 하 고 알려진 문제를 방지할 수 있습니다.

### <a name="how-this-article-is-organized"></a>이 문서의 구성 방법

Azure에서 인프라 및 응용 프로그램 보호를 시작 하는 것은 쉽지만, 기본 Azure 리소스를 올바르게 설정 하 고 최적으로 사용 하도록 하는 경우 값에 시간을 단축할 수 있습니다. 이 문서에서는 Azure Backup 배포를 최적으로 구성 하기 위한 디자인 고려 사항 및 지침을 간략하게 설명 합니다. 핵심 구성 요소 (예: Recovery Services 자격 증명 모음, 백업 정책) 및 개념 (예: 거 버 넌 스) 및 개념 (예: 거 버 넌 스)을 검토 하 고 해당 기능 및 해당 기능을 자세한 제품 설명서에 대 한 링크로

## <a name="architecture"></a>Architecture

![Azure Backup 아키텍처](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>워크로드

Azure Backup를 사용 하면 다양 한 워크 로드 (온-프레미스 및 클라우드)에 대해 데이터를 보호할 수 있습니다. Azure의 안전 하 고 신뢰할 수 있는 기본 제공 데이터 보호 메커니즘입니다. 관리 오버 헤드 없이 여러 워크 로드에 걸쳐 보호를 원활 하 게 확장할 수 있습니다. PowerShell, CLI, Azure Resource Manager 템플릿 및 REST Api를 통해 여러 automation 채널을 사용 하도록 설정 하는 것도 가능 합니다.

* **확장 가능, 내구성 및 보안 저장소-** Azure Backup는 작성 된 보안 및 고가용성 기능을 갖춘 안정적인 Blob storage를 사용 합니다. 백업 데이터에 대해 LRS, GRS 또는 RA-GRS 저장소를 선택할 수 있습니다.  

* **기본 워크 로드 통합-** Azure Backup는 Azure 워크 로드 (Vm, SAP HANA, Azure Vm의 SQL 및 Azure Files)와의 기본 통합을 제공 하 여 에이전트를 배포 하거나, 새 스크립트를 작성 하거나, 저장소를 프로 비전 하는 자동화 또는 인프라를 관리 하지 않아도 됩니다.

### <a name="data-plane"></a>데이터 평면

* **자동화 된 storage management** Azure Backup – 백업 데이터의 저장소 계정 프로 비전 및 관리를 자동화 하 여 백업 데이터가 증가 함에 따라 확장 되도록 합니다.

* **악성 삭제 방지 –** 백업 일시 삭제를 통해 백업을 삭제 하는 실수 및 악의적 시도 로부터 보호 합니다. 삭제 된 백업 데이터는 14 일 동안 무료로 저장 되며이 상태에서 복구 될 수 있습니다.

* **암호화 된 백업 보안-** Azure Backup는 RBAC 및 암호화와 같은 Azure 플랫폼의 기본 제공 보안 기능을 활용 하 여 백업 데이터가 안전한 방식으로 저장 되도록 합니다.

* **백업 데이터 수명 주기 관리-** Azure Backup는 보존 정책을 준수 하기 위해 이전 백업 데이터를 자동으로 정리 합니다. 또한 운영 저장소에서 자격 증명 모음 저장소로 데이터를 계층화 할 수 있습니다.

### <a name="management-plane"></a>관리 평면

* **액세스 제어** – Recovery Services 자격 증명 모음은 관리 기능을 제공 하 고 AZURE PORTAL, SDK, CLI 및 심지어 REST api를 통해 액세스할 수 있습니다. 또한 승인 된 백업 관리자 에게만 백업에 대 한 액세스를 제한 하는 옵션을 제공 하는 RBAC 경계입니다.

* **정책 관리** – 각 자격 증명 모음 내의 Azure Backup 정책은 백업이 트리거되는 시기 및 유지 해야 하는 기간을 정의 합니다. 또한 이러한 정책을 관리 하 고 여러 항목에서 적용할 수 있습니다.

* **모니터링 및 보고** – Azure Backup Log Analytics와 통합 되며 통합 문서를 통해 보고서를 볼 수 있는 기능을 제공 합니다.

* **스냅숏 관리** – Azure Backup 일부 Azure 기본 작업 (vm 및 Azure Files)에 대 한 스냅숏을 생성 하 고 이러한 스냅숏을 관리 하며 이러한 스냅숏을 신속 하 게 복원할 수 있습니다. 이 옵션은 데이터를 원본 저장소로 복구 하는 데 걸리는 시간을 크게 줄여 줍니다.

## <a name="vault-considerations"></a>자격 증명 모음 고려 사항

Azure Backup은 Recovery Services 자격 증명 모음을 사용하여 백업을 오케스트레이션 및 관리합니다. 또한 자격 증명 모음을 사용하여 백업 데이터를 저장합니다. 효과적인 자격 증명 모음 디자인을 통해 조직은 비즈니스 우선 순위를 지원 하기 위해 Azure에서 백업 자산을 구성 하 고 관리 하는 구조를 구축할 수 있습니다. 자격 증명 모음을 만들 때 다음 지침을 고려 하세요.  

### <a name="align-to-subscription-design-strategy"></a>구독 디자인 전략에 맞추기

자격 증명 모음이 구독으로 범위가 지정 되므로 자격 증명 모음 디자인은 특정 응용 프로그램이 나 서비스에 따라 또는 응용 프로그램 archetype 줄을 따라 분리 된 *응용 프로그램 범주 전략과* 같은 구독 디자인 전략을 충족 하는 데 적합 합니다. 자세한 내용은 [이 문서](/azure/cloud-adoption-framework/decision-guides/subscriptions/)를 참조하세요.

### <a name="single-or-multiple-vault"></a>단일 또는 여러 자격 증명 모음

단일 자격 증명 모음 또는 여러 자격 증명 모음을 사용 하 여 백업을 구성 하 고 관리할 수 있습니다. 다음 지침을 고려하세요.

* 워크 로드가 모두 단일 구독과 단일 리소스에 의해 관리 되는 경우 단일 자격 증명 모음을 사용 하 여 백업 공간을 모니터링 하 고 관리할 수 있습니다.

* 워크 로드가 여러 구독에 걸쳐 분산 된 경우 구독 당 하나 이상의 자격 증명 모음을 만들 수 있습니다.
  * 모든 자격 증명 모음, 구독 및 테 넌 트에서 작업 활동의 모니터링을 간소화 하기 위해 Backup 탐색기 및 보고서를 사용할 수 있습니다. 집계 보기를 보려면 [여기에서 자세히 알아보세요](monitor-azure-backup-with-backup-explorer.md) .
  * 자격 증명 모음에서 일관 된 정책을 필요로 하는 경우 Azure policy를 사용 하 여 여러 자격 증명 모음에 백업 정책을 전파할 수 있습니다. [' Deployifnotexists '](../governance/policy/concepts/effects.md#deployifnotexists) 효과를 사용 하 여 여러 자격 증명 모음에 백업 정책을 전파 하는 사용자 지정 [Azure Policy 정의](../governance/policy/concepts/definition-structure.md) 를 작성할 수 있습니다. 할당 하면이 Azure Policy 정의를 특정 범위 (구독 또는 RG)에 [할당할](../governance/policy/assign-policy-portal.md) 수 있으므로 Azure Policy 할당 범위에서 모든 Recovery Services 자격 증명 모음에 ' 백업 정책 ' 리소스를 배포할 수 있습니다. 백업 빈도, 보존 등의 백업 정책 설정은 사용자가 Azure Policy 할당에서 매개 변수로 지정 해야 합니다.

* 조직의 공간이 증가 함에 따라 백업 정책에 따라 정렬, 자격 증명 모음 통합, 저렴 한 중복성에 대 한 절충 (GRS에서 LRS로 이동) 등의 이유로 작업을 구독 간에 이동 하는 것이 좋습니다.  Azure Backup는 Azure 구독에서 또는 동일한 구독 내의 다른 리소스 그룹으로 Recovery Services 자격 증명 모음 이동을 지원 합니다. [자세한 내용은 여기를 참조](backup-azure-move-recovery-services-vault.md)하세요.

### <a name="review-default-settings"></a>기본 설정 검토

저장소 복제 유형 및 보안 설정에 대 한 기본 설정을 검토 하 여 요구 사항을 충족 하 고 자격 증명 모음에서 백업을 구성 합니다.

* 기본적으로 *저장소 복제 유형은* 지역 중복 (GRS)으로 설정 됩니다. 백업을 구성한 후에는 수정 하는 옵션을 사용할 수 없습니다. 설정을 검토 하 고 수정 하려면 [다음](backup-create-rs-vault.md#set-storage-redundancy) 단계를 수행 합니다.

* 새로 만든 자격 증명 모음에 대해 기본적으로 *일시 삭제* 를 사용 하 여 실수로 인 한 삭제 또는 악의적인 삭제 로부터 백업 데이터를 보호 합니다. 설정을 검토 하 고 수정 하려면 [다음](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 단계를 수행 합니다.

* *지역 간 복원을* 사용 하면 azure 쌍을 이루는 지역에 해당 하는 보조 지역에서 azure vm을 복원할 수 있습니다. 이 옵션을 사용 하면 감사 또는 규정 준수 요구 사항을 충족 하는 드릴을 수행 하 고 주 지역에 재해가 발생 한 경우 VM 또는 해당 디스크를 복원할 수 있습니다. CRR은 모든 GRS 자격 증명 모음에 대 한 옵트인 기능입니다. [자세한 내용은 여기를 참조](backup-create-rs-vault.md#set-cross-region-restore)하세요.

* 자격 증명 모음 디자인을 완료 하기 전에 [자격 증명 모음 지원](backup-support-matrix.md#vault-support) 매트릭스를 검토 하 여 디자인 선택에 영향을 주거나 제한 될 수 있는 요인을 파악 합니다.

## <a name="backup-policy-considerations"></a>백업 정책 고려 사항

Azure Backup 정책에는 *일정* (백업 수행 시기) 및 *보존* (백업 보존 기간)의 두 가지 구성 요소가 있습니다. 백업 중인 데이터 유형, RTO/RPO 요구 사항, 운영 또는 규정 준수 요구 사항 및 작업 유형 (예: VM, 데이터베이스, 파일)에 따라 정책을 정의할 수 있습니다. [자세한 내용은 여기를 참조](backup-architecture.md#backup-policy-essentials)하세요.

백업 정책을 만들 때 다음 지침을 고려 하십시오.

### <a name="schedule-considerations"></a>일정 고려 사항

* 단일 정책 내에서 동일한 일정 시작 시간, 빈도 및 보존 설정이 필요한 Vm을 그룹화 하는 것이 좋습니다.

* 백업 예약 시작 시간이 사용량이 많지 않은 프로덕션 응용 프로그램 시간 동안 발생 하는지 확인 합니다.

* 백업 트래픽을 분산 하려면 다른 시간에 다른 Vm을 백업 하 고 시간이 겹치지 않는지 확인 하십시오.

### <a name="retention-considerations"></a>보존 고려 사항

* 단기 보존은 "분" 또는 "매일" 일 수 있습니다. "주별", "월별" 또는 "매년" 백업 지점의 보존은 장기 보존 이라고 합니다.

* 장기 보존:
  * 계획 됨 (준수 요구 사항)-현재 시간에서 데이터가 필요한 것을 미리 알고 있는 경우 장기 보존을 사용 합니다.
  * 계획 되지 않음 (주문형 요구 사항)-미리 알지 못하는 경우 특정 사용자 지정 보존 설정과 함께 주문형으로 사용할 수 있습니다 (이러한 사용자 지정 보존 설정은 정책 설정의 영향을 받지 않음).

* 사용자 지정 보존을 통한 주문형 백업-백업 정책을 통해 예약 되지 않은 백업을 수행 해야 하는 경우 요청 시 백업을 사용할 수 있습니다. 이는 예약 된 백업에 적합 하지 않은 백업이 나 세분화 된 백업을 수행 하는 데 유용할 수 있습니다. 예를 들어 예약 된 백업은 하루에 하나의 백업만 허용 하므로 매일 여러 IaaS VM을 백업 합니다. 예약 정책에 정의 된 보존 정책은 주문형 백업에 적용 되지 않는다는 점에 유의 해야 합니다.

### <a name="optimize-backup-policy"></a>백업 정책 최적화

* 비즈니스 요구 사항이 변경 되 면 보존 기간을 연장 하거나 줄여야 할 수도 있습니다. 이렇게 하면 다음을 예측할 수 있습니다.  
  * 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 표시되고 유지됩니다.
  * 보존 기간을 줄이면 다음 정리 작업에서 복구 지점이 정리 되도록 표시 되 고 이후에 삭제 됩니다.
  * 최신 보존 규칙은 모든 보존 시점 (주문형 보존 지점은 제외)에 적용 됩니다. 따라서 보존 기간이 연장 된 경우 (예: 100 일), 백업이 수행 된 후 보존 감소 (예: 100 일 ~ 7 일)에 따라 모든 백업 데이터는 마지막으로 지정 된 보존 기간 (7 일)에 따라 보존 됩니다.

* Azure Backup는 *백업을 보호 하 고 관리할*수 있는 유연성을 제공 합니다.
  * *보호를 중지 하 고 백업 데이터를 보존*합니다. 데이터 원본 (VM, 응용 프로그램)을 사용 중지 하거나 서비스 해제 하는 경우 감사 또는 규정 준수를 위해 데이터를 유지 해야 하는 경우이 옵션을 사용 하 여 이후의 모든 백업 작업에서 데이터 원본을 보호 하 고 백업 된 복구 지점이 유지 되지 않도록 할 수 있습니다. 그런 다음 VM 보호를 복원 하거나 다시 시작할 수 있습니다.
  * *보호를 중지 하 고 백업 데이터를 삭제*합니다. 이 옵션을 선택 하면 이후의 모든 백업 작업이 VM을 보호 하 고 모든 복구 지점이 삭제 되지 않습니다. VM을 복원 하거나 백업 다시 시작 옵션을 사용할 수 없습니다.

  * 데이터 보관을 사용 하 여 중지 된 데이터 원본의 보호를 다시 시작 하는 경우 보존 규칙이 적용 됩니다. 예약 된 시간에 만료 된 복구 지점이 제거 됩니다.

* 정책 디자인을 완료 하기 전에 디자인 선택에 영향을 줄 수 있는 다음 요소에 대해 알고 있어야 합니다.
  * 백업 정책은 자격 증명 모음으로 범위가 지정 됩니다.
  * 정책 당 항목 수에는 제한이 있습니다 (예: 100 Vm). 크기를 조정 하려면 동일한 일정 또는 다른 일정으로 중복 된 정책을 만들 수 있습니다.
  * 특정 복구 지점은 선택적으로 삭제할 수 없습니다.
  * 예약 된 백업을 완전히 사용 하지 않도록 설정 하 고 데이터 원본을 보호 된 상태로 유지할 수 없습니다. 정책을 사용 하 여 구성할 수 있는 가장 낮은 백업에는 매주 예약 된 백업이 하나 있습니다. 또는 백업을 수행 하 고, 주문형 백업을 수행 하 고, 보호를 해제 하 고, 백업 데이터를 보존 하려면 데이터 보존을 사용 하 여 보호를 중지 하 고 보호를 사용 하도록 설정 하는 것이 좋습니다. [자세한 내용은 여기를 참조](backup-azure-manage-vms.md#stop-protecting-a-vm)하세요.

## <a name="security-considerations"></a>보안 고려 사항

백업 데이터를 보호 하 고 비즈니스의 보안 요구를 충족 하는 데 도움이 되도록, Azure Backup는 중요 한 데이터 및 시스템의 고의적인 공격과 남용에 대해 기밀성, 무결성 및 가용성 보증을 제공 합니다. Azure Backup 솔루션에 대 한 다음 보안 지침을 고려 하세요.

### <a name="authentication-and-authorization"></a>인증 및 권한 부여

* Azure RBAC (역할 기반 액세스 제어)는 팀 내에서 분리 업무를 수행 하는 데 필요한 사용자 에게만 액세스 권한을 부여 하 고 세분화 된 액세스 관리를 가능 하 게 합니다. [자세한 내용은 여기를 참조](backup-rbac-rs-vault.md)하세요.

* Azure Backup는 백업 관리 작업을 제어 하기 위한 세 가지 기본 제공 역할인 백업 참가자, 운영자 및 판독기를 제공 합니다. [자세한 내용은 여기를 참조](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)하세요.

* Azure Backup에는 보안 취약성을 예방, 감지 및 대응 하기 위해 서비스에 기본 제공 되는 몇 가지 보안 컨트롤이 있습니다 (자세한 정보).

* Recovery Services 자격 증명 모음에 사용 되는 저장소 계정은 격리 되어 있으며 사용자가 악의적인 목적으로 액세스할 수 없습니다. 액세스는 복원과 같은 Azure Backup 관리 작업을 통해서만 허용됩니다.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>전송 중 및 미사용 데이터 암호화

암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다.

* Azure 내에서 Azure 스토리지와 자격 증명 모음 간에 이동하는 데이터는 HTTPS를 통해 보호됩니다. 이 데이터는 Azure 네트워크 내에 유지 됩니다.

* 백업 데이터는 Microsoft 관리 키를 사용하여 자동으로 암호화됩니다. 또는 [고객이 관리 하는 키](encryption-at-rest-with-cmk.md)라고도 하는 고유한 키를 사용할 수 있습니다.

* Azure Backup은 ADE(Azure Disk Encryption)로 암호화된 OS/데이터 디스크가 있는 Azure VM의 백업 및 복원을 지원합니다. [자세한 내용은 여기를 참조](backup-azure-vms-encryption.md)하세요.

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>의도하지 않은 삭제로부터 백업 데이터 보호

Azure Backup은 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다. 일시 삭제를 사용 하는 경우 사용자가 백업 (VM, SQL Server 데이터베이스, Azure 파일 공유 SAP HANA 데이터베이스)을 삭제 하면 백업 데이터는 14 일 동안 보존 되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대 한 14 일의 추가 보존은 비용이 발생 하지 않습니다. [자세한 내용은 여기를 참조](backup-azure-security-feature-cloud.md)하세요.

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>의심스러운 활동의 모니터링 및 경고

Azure Backup은 Azure Backup과 관련된 이벤트에 대한 작업을 보고 구성할 수 있는 기본 제공 모니터링 및 경고 기능을 제공합니다. [자세한 내용은 여기를 참조](security-overview.md#monitoring-and-alerts-of-suspicious-activity)하세요.

### <a name="security-features-to-help-protect-hybrid-backups"></a>하이브리드 백업을 보호하기 위한 보안 기능

Azure Backup 서비스는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업하고 복원합니다. 이제 MARS는 보안 기능을 제공 합니다. 즉, Azure Backup에서 다운로드 한 후 업로드 하 고 암호 해독 하기 전에 암호화할 암호를 제공 합니다. 삭제 된 백업 데이터는 삭제 날짜부터 14 일 동안 보존 되며 중요 한 작업 (예: 암호 변경)은 유효한 Azure 자격 증명이 있는 사용자만 수행할 수 있습니다. [자세한 내용은 여기를 참조](backup-azure-security-feature.md)하세요.

## <a name="network-considerations"></a>네트워크 고려 사항

Azure Backup 작업에서 Recovery Services 자격 증명 모음으로 데이터를 이동 해야 합니다. Azure Backup는 백업 데이터가 실수로 노출 되지 않도록 보호 하는 몇 가지 기능을 제공 합니다 (예: 네트워크의 메시지 가로채기 (man-in-the-middle) 공격). 다음 지침을 고려하세요.

### <a name="internet-connectivity"></a>인터넷 연결

* *AZURE VM backup* -저장소와 Azure Backup 서비스 간의 모든 필수 통신 및 데이터 전송은 가상 네트워크에 액세스할 필요 없이 Azure 네트워크 내에서 발생 합니다. 따라서 보안 네트워크 내에 배치 된 Azure Vm을 백업 하는 경우 Ip 또는 Fqdn에 대 한 액세스를 허용할 필요가 없습니다.

* Azure vm의 *SAP HANA 데이터베이스, AZURE vm의 SQL Server 데이터베이스* -Azure Backup 서비스, Azure Storage 및 Azure Active Directory에 대 한 연결이 필요 합니다. 프라이빗 엔드포인트를 사용하거나 필요한 공용 IP 주소 또는 FQDN에 대한 액세스를 허용하면 됩니다. 필요한 Azure 서비스에 대한 적절한 연결을 허용하지 않으면 데이터베이스 검색, 백업 구성, 백업 수행, 데이터 복원과 같은 작업 시 오류가 발생할 수 있습니다. NSG 태그, Azure 방화벽 및 HTTP 프록시를 사용 하는 동안 전체 네트워크 지침은 이러한 [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) 및 [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) 문서를 참조 하세요.

* *하이브리드* -MARS (Microsoft Azure Recovery Services) 에이전트에는 모든 중요 작업 (설치, 구성, 백업 및 복원)을 위한 네트워크 액세스가 필요 합니다. MARS 에이전트는 공용 피어 링 (이전 회로에 사용 가능) 및 Microsoft 피어 링 [을 사용 하](install-mars-agent.md#private-endpoints) 여 [Azure express](install-mars-agent.md#use-azure-expressroute) 경로를 통해 Azure express 경로에 Azure Backup 연결할 수 [있습니다.](install-mars-agent.md#verify-internet-access)

### <a name="private-endpoints-for-azure-backup"></a>Azure Backup에 대 한 개인 끝점

Azure [개인 끝점](../private-link/private-endpoint-overview.md) 은 Azure 개인 링크를 통해 제공 되는 서비스에 비공개로 안전 하 게 연결 하는 네트워크 인터페이스입니다. Azure Backup를 사용 하면 개인 끝점을 사용 하 여 Recovery Services 자격 증명 모음에서 데이터를 안전 하 게 백업 하 고 복원할 수 있습니다.

* 자격 증명 모음에 대 한 개인 끝점을 사용 하도록 설정 하는 경우 Azure VM 및 MARS 에이전트 백업의 SQL 및 SAP HANA 워크 로드의 백업 및 복원에만 사용 됩니다.  다른 워크 로드의 백업에도 자격 증명 모음을 사용할 수 있습니다. 단, 개인 끝점은 필요 하지 않습니다. MARS 에이전트를 사용 하 여 SQL 및 SAP HANA 작업과 백업 백업 외에도 개인 끝점은 Azure VM 백업 시 파일 복구를 수행 하는 데 사용 됩니다. [자세한 내용은 여기를 참조](private-endpoints.md#recommended-and-supported-scenarios)하세요.

* Azure Active Directory는 현재 개인 끝점을 지원 하지 않습니다. 따라서 Azure Active Directory에 필요한 Ip 및 Fqdn은 Azure Vm에서 데이터베이스의 백업을 수행 하 고 MARS 에이전트를 사용 하 여 백업할 때 보안 네트워크에서의 아웃 바운드 액세스를 허용 해야 합니다. 해당 하는 경우 Azure AD에 대 한 액세스를 허용 하기 위해 NSG 태그 및 Azure 방화벽 태그를 사용할 수도 있습니다. [여기에서 필수 구성 요소](./private-endpoints.md#before-you-start)에 대해 자세히 알아보세요.

## <a name="governance-considerations"></a>거버넌스 고려 사항

Azure의 거 버 넌 스는 주로 [Azure Policy](../governance/policy/overview.md) 및 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 사용 하 여 구현 됩니다. [Azure Policy](../governance/policy/overview.md)를 사용하면 정책 정의를 만들고 할당하고 관리하여 리소스에 대 한 규칙을 적용할 수 있습니다. 이 기능은 해당 리소스가 회사 표준을 준수하게 합니다. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 사용하면 Azure 리소스 및 기타 클라우드 공급 기업에 대한 클라우드 사용량 및 비용을 추적할 수 있습니다. 또한 [Azure Price 계산기](https://azure.microsoft.com/pricing/calculator/) 및 [Azure Advisor](../advisor/advisor-overview.md)  같은 다음 도구는 cost management 프로세스에서 중요 한 역할을 합니다.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup 기본 제공 Azure Policy를 통해 두 가지 주요 시나리오를 지원 합니다.

* 새로 만든 업무상 중요 한 컴퓨터가 올바른 보존 설정으로 자동으로 백업 되도록 합니다. Azure Backup는 구독 또는 리소스 그룹 내의 지정 된 위치에 있는 모든 Azure Vm에 할당 될 수 있는 기본 제공 정책 (Azure Policy 사용)을 제공 합니다. 이 정책이 지정 된 범위에 할당 되 면 해당 범위에서 만들어진 모든 새 Vm은 동일한 위치 및 구독의 기존 자격 증명 모음에 백업 하도록 자동으로 구성 됩니다. 사용자는 백업 된 Vm을 연결할 자격 증명 모음 및 보존 정책을 지정할 수 있습니다. [자세한 내용은 여기를 참조](backup-azure-auto-enable-backup.md)하세요.

* 새로 만든 자격 증명 모음이 보고서를 지원 하기 위해 진단을 사용 하도록 설정 되었는지 확인 합니다. 종종 자격 증명 모음 마다 진단 설정을 수동으로 추가 하는 작업은 복잡할 수 있습니다. 또한 생성 된 모든 새 자격 증명 모음에 대 한 보고서를 볼 수 있도록 진단 설정을 사용 하도록 설정 해야 합니다. Log Analytics를 대상으로 사용 하 여 대규모로 진단 설정 만들기를 간소화 하기 위해 Azure Backup 기본 제공 Azure Policy를 제공 합니다. 이 정책은 각 구독 또는 리소스 그룹의 모든 자격 증명 모음에 LA 진단 설정을 추가 합니다. 다음 섹션에서는이 정책을 사용 하는 방법에 대 한 지침을 제공 합니다. [자세한 내용은 여기를 참조](azure-policy-configure-diagnostics.md)하세요.

### <a name="azure-backup-cost-considerations"></a>Azure Backup 비용 고려 사항

Azure Backup 서비스의 기능은 비용을 효과적으로 관리 하 고 BCDR (비즈니스 연속성 및 재해 복구) 비즈니스 요구 사항을 충족 하는 유연성을 제공 합니다. 다음 지침을 고려하세요.

* 가격 계산기를 사용 하 여 다양 한 레버를 조정 하 여 비용을 평가 하 고 최적화할 수 있습니다. [여기서 자세히 알아보세요.](azure-backup-pricing.md)

* Backup 탐색기: backup 탐색기 또는 Backup 보고서를 사용 하 여 백업 저장소 증가를 이해 하 고 최적화 하 여 중요 하지 않은 작업 또는 삭제 된 Vm에 대 한 백업을 중지 합니다. 백업 관점에서 전체 공간에 대 한 집계 보기를 가져올 수 있습니다. 여기에는 백업 항목에 대 한 정보 뿐만 아니라 백업용으로 구성 되지 않은 리소스도 포함 됩니다. 이렇게 하면 증가 하는 공간에서 중요 한 데이터를 보호 하지 않도록 하 고, 중요 하지 않은 작업 또는 삭제 된 워크 로드에 대해 백업을 최적화 합니다.

* 백업 정책 최적화
  * 작업 archetype (예: 중요 업무용, 중요 하지 않은)에 따라 일정 및 보존 설정 최적화
  * 즉시 복원에 대 한 보존 설정 최적화
  * 요구 사항에 맞게 올바른 백업 유형을 선택 하 고, Azure Backup에서 워크 로드에 대해 지원 되는 백업 유형 (전체, 증분, 로그, 차등)을 고려 합니다.

* 선택적 백업 디스크: 디스크 제외 (미리 보기 기능)는 중요 한 데이터를 선택적으로 백업 하는 효율적이 고 비용 효율적인 선택 항목을 제공 합니다. 예를 들어 VM에 연결 된 나머지 디스크를 백업 하지 않으려는 경우 하나의 디스크만 백업 합니다. 이는 여러 백업 솔루션이 있는 경우에도 유용 합니다. 예를 들어 워크 로드 백업 솔루션을 사용 하 여 데이터베이스 또는 데이터를 백업 하는 경우 (Azure VM 백업의 SQL Server 데이터베이스), 선택한 디스크에 대해 Azure VM 수준 백업을 사용 하려는 경우를 예로 들 수 있습니다.

* Azure Backup은 Azure Vm의 스냅숏을 만들어 디스크와 함께 저장 하 여 복구 지점 만들기를 강화 하 고 복원 작업의 속도를 향상 시킵니다. 이를 인스턴트 복원 이라고 합니다. 기본적으로 인스턴트 복원 스냅숏은 2 일 동안 유지 됩니다. 이 기능을 사용 하면 복원 시간을 줄여서 이러한 스냅숏의 복원 작업을 수행할 수 있습니다. 자격 증명 모음에서 데이터를 다시 변환 하 고 복사 하는 데 필요한 시간을 줄일 수 있습니다. 따라서 이 기간에 만든 스냅샷에 해당하는 스토리지 비용이 발생합니다. [자세한 내용은 여기를 참조](backup-instant-restore-capability.md#configure-snapshot-retention)하세요.

* 기본적으로 Azure Backup 자격 증명 모음의 저장소 복제 유형은 지역 중복 (GRS)으로 설정 됩니다. 항목을 보호 한 후에는이 옵션을 변경할 수 없습니다. GRS (지역 중복 저장소)는 LRS (로컬 중복 저장소) 보다 높은 수준의 데이터 내 구성을 제공 하므로 옵트인에서 지역 간 복원과 비용을 더 많이 사용할 수 있습니다. 저렴 한 비용과 높은 데이터 내구성 간의 장단점을 검토 하 고 시나리오에 가장 적합 한 것을 결정 합니다. [여기서 자세히 알아보세요.](backup-create-rs-vault.md#set-storage-redundancy)

* VM 및 VM 자체 내에서 실행 되는 워크 로드를 모두 보호 하는 경우이 이중 보호가 필요한 지 확인 합니다.

## <a name="monitoring-and-alerting-considerations"></a>모니터링 및 경고 고려 사항

백업 사용자 또는 관리자는 모든 백업 솔루션을 모니터링 하 고 중요 한 시나리오에 대 한 알림 메시지를 받을 수 있어야 합니다. 이 섹션에서는 Azure Backup 서비스에서 제공 하는 모니터링 및 알림 기능에 대해 자세히 설명 합니다.

### <a name="monitoring"></a>모니터링

* Azure Backup는 백업 구성, 백업, 복원, 삭제 등의 작업에 대해 **빌드된 작업 모니터링 기능** 을 제공 합니다. 이는 자격 증명 모음으로 범위가 지정 되 고 단일 자격 증명 모음 모니터링에 적합 합니다. [자세한 내용은 여기를 참조](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)하세요.

* 규모에 따라 작업 활동을 모니터링 해야 하는 경우 **백업 탐색기** 는 전체 백업 공간에 대 한 집계 보기를 제공 하 여 세부적인 드릴 다운 분석과 문제 해결을 가능 하 게 합니다. Azure의 전체 백업 공간에서 작업 활동을 모니터링 하 고 테 넌 트, 위치, 구독, 리소스 그룹 및 자격 증명 모음을 확장 하는 데 사용할 수 있는 단일 중앙 위치를 제공 하는 기본 제공 Azure Monitor 통합 문서입니다. [자세한 내용은 여기를 참조](monitor-azure-backup-with-backup-explorer.md)하세요.
  * 이를 사용 하 여 백업에 대해 구성 되지 않은 리소스를 식별 하 고 증가 하는 공간에서 중요 한 데이터를 보호 하지 않도록 합니다.
  * 대시보드는 지난 7 일간 (최대) 작업 활동을 제공 합니다. 이 데이터를 유지 해야 하는 경우 Excel 파일로 내보낸 후 유지할 수 있습니다.
  * Azure Lighthouse 사용자 인 경우 여러 테 넌 트 간에 정보를 보고 경계 없는 모니터링을 사용할 수 있습니다.

* 장기 작업을 유지 하 고 확인 해야 하는 경우 **보고서**를 사용 합니다. 백업 관리자의 일반적인 요구 사항은 오랜 기간에 걸쳐 있는 데이터를 기반으로 백업에 대 한 정보를 얻는 것입니다. 이러한 솔루션에 대한 사용 사례는 다음과 같습니다.
  * 사용된 클라우드 스토리지를 할당하고 예측합니다.
  * 백업 및 복원을 감사합니다.
  * 여러 세분성 수준에서 주요 추세를 확인합니다.

* 기타
  * **Log Analytics** 작업 영역에 데이터 (예: 작업, 정책 등)를 보낼 수 있습니다. 이렇게 하면 Azure Monitor 로그의 기능을 사용 하 여 Azure Monitor에서 수집 된 다른 모니터링 데이터와 데이터의 상관 관계를 설정 하 고, 여러 Azure 구독 및 테 넌 트의 로그 항목을 분석을 위해 하나의 위치로 통합 하 고, 로그 쿼리를 사용 하 여 복잡 한 분석을 수행 하 고 로그 항목에 대 한 심층 통찰력을 얻을 [자세한 내용은 여기를 참조](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)하세요.
  * 데이터를 이벤트 허브로 전송 하 여 타사 SIEM (보안 정보 및 이벤트 관리) 또는 다른 log analytics 솔루션과 같은 Azure 외부에 항목을 보낼 수 있습니다. [자세한 내용은 여기를 참조](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs)하세요.
  * 감사, 정적 분석 또는 백업을 위해 90 일 보다 오래 된 로그 데이터를 보존 하려면 Azure Storage 계정으로 데이터를 보낼 수 있습니다. 90 일 이내에 이벤트를 유지 해야 하는 경우 활동 로그 이벤트는 90 일 동안 Azure 플랫폼에 보관 되므로 저장소 계정에 보관을 설정할 필요가 없습니다. [자세히 알아보기](../azure-monitor/platform/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>경고

* 경고는 주로 관련 작업을 수행 하 라는 알림을 받는 방법입니다. 백업 경고 섹션에는 Azure Backup 서비스에 의해 생성 된 경고가 표시 됩니다.

* Azure Backup는 오류, 경고 및 중요 한 작업을 위해 전자 메일을 통해 **빌드된 경고** 알림 메커니즘을 제공 합니다. 경고가 생성 될 때 알림을 받을 개별 메일 주소 또는 메일 그룹을 지정할 수 있습니다. 각 경고에 대 한 알림을 받을지 또는 매시간 다이제스트로 그룹화 한 다음 알림을 받을 지를 선택할 수도 있습니다.
  * 이러한 경고는 서비스에 의해 정의 되며 제한 된 시나리오에 대 한 지원을 제공 합니다. 백업/복원 실패, 데이터 보관/데이터 삭제로 보호 중지 등의 제한 된 시나리오에 대 한 지원을 제공 합니다. [자세한 내용은 여기를 참조](backup-azure-monitoring-built-in-monitor.md#alert-scenarios)하세요.
  * 데이터 삭제로 보호 중지와 같은 파괴적인 작업을 수행 하면 경고가 발생 하 고 Recovery Services 자격 증명 모음에 대해 알림이 구성 **되지 않은** 경우에도 전자 메일이 구독 소유자, 관리자 및 공동 관리자에 게 전송 됩니다.
  * 특정 워크 로드는 오류 (예: 15 분 마다 SQL Server)의 높은 빈도를 생성할 수 있습니다. 각 실패 발생에 대해 발생 하는 경고를 지나치게 많이 방지 하기 위해 경고가 통합 됩니다. [자세한 내용은 여기를 참조](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts)하세요.
  * 작성 된 경고는 사용자 지정할 수 없으며 Azure Portal에 정의 된 전자 메일로 제한 됩니다.

* **사용자 지정 경고를 만들어야** 하는 경우 (예: 성공한 작업에 대 한 경고) Log Analytics 사용 합니다. Azure Monitor에서는 Log Analytics 작업 영역에 고유 경고를 만들 수 있습니다. 하이브리드 워크 로드 (DPM/MABS)는 데이터를 LA로 보내고 LA를 사용 하 여 Azure Backup에서 지 원하는 워크 로드 간에 일반적인 경고를 제공할 수도 있습니다.

* 또한 기본 제공 Recovery Services 자격 증명 모음 **활동 로그**를 통해 알림을 받을 수 있습니다. 그러나 제한 된 시나리오를 지원 하며 예약 된 백업과 같은 작업에 적합 하지 않습니다 .이는 활동 로그 보다 리소스 로그에 더 잘 부합 합니다. 이러한 제한 사항 및 Azure Backup에서 보호 되는 모든 워크 로드에 대 한 규모의 모니터링 및 경고에 Log Analytics 작업 영역을 사용할 수 있는 방법에 대 한 자세한 내용은이 [문서](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Backup 사용을 위한 시작 점으로 다음 문서를 참조 하는 것이 좋습니다.

* [Azure Backup 개요](backup-overview.md)
* [질문과 대답](backup-azure-backup-faq.md)
