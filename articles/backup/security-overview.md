---
title: 보안 기능 개요
description: 백업 데이터를 보호하고 비즈니스의 보안 요구를 충족하는 데 도움이 되는 Azure Backup의 보안 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 62f4e6783d4c2c2e09b7b4cbb41ae98ac2779ebe
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012554"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure Backup의 보안 기능 개요

데이터를 보호하기 위해 수행할 수 있는 가장 중요한 단계 중 하나는 안정적인 백업 인프라를 보유하는 것입니다. 그러나 데이터를 안전한 방식으로 백업하고 백업이 항상 보호되도록 하는 것이 중요합니다. Azure Backup은 데이터를 전송 중이거나 휴지 상태일 때 백업 환경에 대한 보안을 제공합니다. 이 문서는 백업 데이터를 보호하고 비즈니스의 보안 요구를 충족하는 데 도움이 되는 Azure Backup의 보안 기능을 나열합니다.

## <a name="management-and-control-of-identity-and-user-access"></a>ID 및 사용자 액세스 관리 및 제어

Recovery Services 자격 증명 모음에 사용 되는 저장소 계정은 격리 되어 있으며 사용자가 악의적인 목적으로 액세스할 수 없습니다. 액세스는 복원과 같은 Azure Backup 관리 작업을 통해서만 허용됩니다. Azure Backup를 사용 하면 azure [RBAC (역할 기반 액세스 제어)](./backup-rbac-rs-vault.md)를 사용 하 여 세분화 된 액세스를 통해 관리 되는 작업을 제어할 수 있습니다. RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

Azure Backup은 백업 관리 작업을 제어할 수 있는 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 3개 제공합니다.

* Backup 기여자 - Recovery Services 자격 증명 모음을 삭제하고 다른 사용자에 대한 액세스 권한을 제공하는 권한을 제외하고, 백업을 만들고 관리
* Backup 운영자 - 백업을 제거하고 백업 정책을 관리하는 권한을 제외하고, 기여자가 하는 모든 일
* Backup 읽기 권한자 - 모든 백업 관리 작업을 볼 수 있는 권한

[Azure Backup을 관리하는 역할 기반 액세스 제어](./backup-rbac-rs-vault.md)에 대해 자세히 알아보세요.

Azure Backup에는 보안 취약성을 예방, 감지 및 대응하기 위해 서비스에 기본 제공되는 몇 가지 보안 컨트롤이 있습니다. [Azure Backup에 대한 보안 제어](./backup-security-controls.md)에 대해 자세히 알아보세요.

## <a name="separation-between-guest-and-azure-storage"></a>게스트와 Azure 스토리지 간의 분리

가상 머신 백업 및 VM 백업의 SQL과 SAP HANA를 포함하는 Azure Backup을 사용하여 백업 데이터는 Azure 스토리지에 저장되며 게스트는 백업 스토리지 또는 해당 콘텐츠에 직접 액세스할 수 없습니다.  가상 머신 백업을 사용하여 백업 스냅샷 만들기 및 스토리지는 게스트에서 애플리케이션 일치 백업에 대한 워크로드를 정지하는 것 외에는 참여하지 않는 Azure 패브릭에 의해 수행됩니다.  SQL 및 SAP HANA를 사용하는 경우 백업 확장 프로그램은 특정 Blob에 쓰기 위한 임시 액세스 권한을 가집니다.  이러한 방식으로 손상된 환경에서도 기존 백업은 게스트에 의해 변조 또는 삭제될 수 없습니다.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 백업에 인터넷 연결이 필요하지 않음

Azure VM을 백업하려면 가상 머신의 디스크에서 Recovery Services 자격 증명 모음으로 데이터를 이동해야 합니다. 그러나 모든 필수 통신 및 데이터 전송은 가상 네트워크에 액세스할 필요 없이 Azure 백본 네트워크에서만 수행됩니다. 따라서 보안 네트워크 내에 배치된 Azure VM을 백업하려면 모든 IP 또는 FQDN에 대한 액세스를 허용하지 않아도 됩니다.

## <a name="private-endpoints-for-azure-backup"></a>Azure Backup에 대 한 개인 끝점

이제 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 데이터를 가상 네트워크 내의 서버에서 Recovery Services 자격 증명 모음으로 안전하게 백업할 수 있습니다. 개인 끝점은 자격 증명 모음에 대 한 VNET 주소 공간의 IP를 사용 하므로 공용 Ip에 가상 네트워크를 노출할 필요가 없습니다. 프라이빗 엔드포인트는 Azure VM 내에서 실행되는 SQL 및 SAP HANA 데이터베이스를 백업하고 복원하는 데 사용할 수 있습니다. 또한 MARS 에이전트를 사용하는 온-프레미스 서버에도 사용할 수 있습니다.

Azure Backup의 프라이빗 엔드포인트에 대한 자세한 내용은 [여기](./private-endpoints.md)를 참조하세요.

## <a name="encryption-of-data"></a>데이터 암호화

암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다. 데이터 암호화는 Azure Backup의 여러 단계에서 발생 합니다.

* Azure 내에서 Azure storage와 자격 증명 모음 간의 전송 데이터는 [HTTPS에 의해 보호](backup-support-matrix.md#network-traffic-to-azure)됩니다. 이 데이터는 Azure 백본 네트워크에 남아 있습니다.

* 백업 데이터는 [플랫폼 관리 키](backup-encryption.md)를 사용 하 여 자동으로 암호화 되며이를 사용 하도록 설정 하기 위해 명시적인 조치를 취할 필요가 없습니다. Azure Key Vault에 저장 된 [고객 관리 키](encryption-at-rest-with-cmk.md) 를 사용 하 여 백업 된 데이터를 암호화할 수도 있습니다. Recovery Services 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.

* Azure Backup은 [Azure Disk Encryption (ADE)](backup-azure-vms-encryption.md#encryption-support-using-ade) 로 암호화 된 OS/데이터 디스크와 [cmk 암호화 된 디스크가 있는 vm](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys)을 포함 하는 Azure vm의 백업 및 복원을 지원 합니다. 자세한 내용은 [암호화 된 Azure vm 및 Azure Backup에 대해 자세히 알아보세요](./backup-azure-vms-encryption.md).

* MARS 에이전트를 사용 하 여 온-프레미스 서버에서 데이터를 백업 하는 경우 Azure Backup 업로드 하기 전에 암호를 사용 하 여 데이터를 암호화 한 후 Azure Backup에서 다운로드 한 후에만 암호를 해독 합니다. [하이브리드 백업을 보호 하는 데 도움이 되는 보안 기능](#security-features-to-help-protect-hybrid-backups)에 대해 자세히 알아보세요.

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>의도하지 않은 삭제로부터 백업 데이터 보호

Azure Backup은 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다. 일시 삭제를 사용하는 경우 사용자가 VM의 백업을 삭제하면 백업 데이터는 14일 동안 보존되어 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대 한 14 일의 추가 보존은 비용이 발생 하지 않습니다. [일시 삭제에 대해 자세히 알아보세요](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>의심스러운 활동의 모니터링 및 경고

Azure Backup은 Azure Backup과 관련된 이벤트에 대한 작업을 보고 구성할 수 있는 [기본 제공 모니터링 및 경고 기능](./backup-azure-monitoring-built-in-monitor.md)을 제공합니다. [Backup 보고서](./configure-reports.md)는 사용량 추적, 백업 및 복원 감사, 다양한 세분성 수준에서 키 추세 식별을 위한 하나의 중지 대상 역할을 합니다. Azure Backup의 모니터링 및 보고 도구를 사용하면 무단, 의심스러운 또는 악의적인 활동을 발생하는 즉시 사용자에게 경고할 수 있습니다.

## <a name="security-features-to-help-protect-hybrid-backups"></a>하이브리드 백업을 보호하기 위한 보안 기능

Azure Backup 서비스는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업하고 복원합니다. MARS는 이제 하이드리브 백업을 보호하는 데 도움이 되는 보안 기능을 제공합니다. 이러한 기능으로는 다음이 포함됩니다.

* 암호 변경과 같은 중요한 작업이 수행될 때마다 추가적인 인증 계층이 제공됩니다. 이 유효성 검사는 유효한 Azure 자격 증명을 가진 사용자만 이러한 작업을 수행할 수 있는지 확인합니다. [공격을 방지하는 기능에 대해 자세히 알아보세요](./backup-azure-security-feature.md#prevent-attacks).

* 삭제된 백업 데이터는 삭제일로부터 추가 14일 동안 보존됩니다. 이를 통해 지정된 기간 내에는 데이터를 복구할 수 있게 되므로 공격이 발생하더라도 데이터가 손실되지 않습니다. 또한 데이터 손상으로부터 보호하기 위해 더 많은 수의 최소 복구 지점이 유지됩니다. [삭제된 백업 데이터 복구에 대해 자세히 알아보세요](./backup-azure-security-feature.md#recover-deleted-backup-data).

* MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 백업된 데이터의 경우 Azure Backup에서 다운로드한 후에만 Azure Backup에 업로드하고 해독하기 전에 데이터를 암호화하는 데 암호가 사용됩니다. 암호 정보는 암호를 만든 사용자와 해당 암호를 사용 하 여 구성 된 에이전트에 대해서만 사용할 수 있습니다. 서비스와 함께 전송되거나 공유되지 않습니다. 이를 통해 실수로 노출 되는 데이터 (예: 네트워크에 대 한 메시지 가로채기 (man-in-the-middle) 공격)를 암호 없이 사용할 수 없으며, 암호는 네트워크를 통해 전송 되지 않으므로 데이터를 완벽 하 게 보안 할 수 있습니다.

## <a name="compliance-with-standardized-security-requirements"></a>표준화된 보안 요구 사항 준수

Microsoft Azure 및 Azure Backup은 조직이 개인 데이터의 수집 및 사용을 규정하는 국가, 지역 및 산업별 요구 사항을 준수할 수 있도록 포괄적인 인증 및 증명 모음을 제공합니다. [규정 준수 인증 목록 참조](compliance-offerings.md)

## <a name="next-steps"></a>다음 단계

* [Azure Backup을 사용하는 클라우드 워크로드를 보호하는 데 도움이 되는 보안 기능](backup-azure-security-feature-cloud.md)
* [Azure Backup을 사용하는 하이브리드 백업을 보호하는 데 도움이 되는 보안 기능](backup-azure-security-feature.md)
