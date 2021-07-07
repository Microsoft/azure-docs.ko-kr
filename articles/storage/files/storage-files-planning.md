---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포 계획을 이해 합니다. Azure 파일 공유를 직접 탑재 하거나 Azure 파일 동기화를 사용 하 여 Azure 파일 공유를 온-프레미스로 캐시할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 267b68fbdae6d894acc3222a8d74a8e15e865dbc
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023523"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획
[Azure Files](storage-files-introduction.md) 는 서버를 사용 하지 않는 azure 파일 공유를 직접 탑재 하거나 Azure 파일 동기화를 사용 하 여 온-프레미스에서 azure 파일 공유를 캐시 하는 두 가지 주요 방법으로 배포할 수 있습니다. 선택 하는 배포 옵션에 따라 배포를 계획할 때 고려해 야 할 사항이 변경 됩니다. 

- **Azure 파일 공유의 직접 탑재**: AZURE FILES은 SMB (서버 메시지 블록) 또는 Nfs (네트워크 파일 시스템) 액세스를 제공 하므로, OS에서 사용할 수 있는 표준 SMB 또는 nfs 클라이언트를 사용 하 여 온-프레미스 또는 클라우드에서 azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버리스이므로 프로덕션 시나리오를 위해 배포하는 경우 파일 서버 또는 NAS 디바이스를 관리할 필요가 없습니다. 즉 소프트웨어 패치를 적용하거나 실제 디스크를 교환할 필요가 없습니다. 

- **Azure 파일 동기화를 사용하여 온-프레미스에서 Azure 파일 공유 캐시**: Azure 파일 동기화를 사용하면 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화 온-프레미스 (또는 클라우드) Windows Server를 Azure SMB 파일 공유의 빠른 캐시로 변환 합니다. 

이 문서에서는 온-프레미스 또는 클라우드 클라이언트에서 직접 탑재할 Azure 파일 공유를 배포 하기 위한 배포 고려 사항을 주로 다룹니다. Azure 파일 동기화 배포를 계획 하려면 [Azure 파일 동기화 배포에 대 한 계획](storage-sync-files-planning.md)을 참조 하세요.

## <a name="available-protocols"></a>사용 가능한 프로토콜

Azure Files는 파일 공유, SMB 및 NFS (네트워크 파일 시스템)를 탑재할 때 사용할 수 있는 두 가지 프로토콜을 제공 합니다. 이러한 프로토콜에 대 한 자세한 내용은 [Azure 파일 공유 프로토콜](storage-files-compare-protocols.md)을 참조 하세요.

> [!IMPORTANT]
> 이 문서의 내용은 대부분 SMB 공유에만 적용 됩니다. NFS 공유에 적용 되는 모든 항목에는 해당 하는 상태가 적용 됩니다.

## <a name="management-concepts"></a>관리 개념
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Azure 파일 공유를 저장소 계정에 배포할 때는 다음을 수행 하는 것이 좋습니다.

- Azure 파일 공유를 다른 Azure 파일 공유와 함께 저장소 계정에만 배포 합니다. Azure 파일 공유 및 blob 컨테이너와 같은 저장소 리소스가 저장소 계정의 한도를 공유 하기 때문에 저장소 계정을 GPv2 저장소 계정을 사용할 수 있지만, 리소스를 함께 사용 하면 나중에 성능 문제를 해결 하기가 더 어려워질 수 있습니다. 

- Azure 파일 공유를 배포할 때 스토리지 계정의 IOPS 제한 사항에 주의합니다. 파일 공유를 스토리지 계정과 1:1로 매핑하는 것이 가장 좋지만, 조직과 Azure 모두의 다양한 제한과 제약으로 인해 항상 가능한 것은 아닙니다. 하나의 스토리지 계정에 파일 공유를 하나만 배포할 수 없는 경우 많은 작업을 수행하는 공유와 적은 작업을 수행하는 공유를 고려하여 가장 많이 사용하는 파일 공유가 동일한 스토리지 계정에 함께 포함되지 않도록 하십시오.

- 사용자 환경에서 GPv2 및 FileStorage 계정만 배포 하 고 GPv1 및 클래식 저장소 계정도 업그레이드 합니다. 

## <a name="identity"></a>ID
Azure 파일 공유에 액세스 하려면 파일 공유의 사용자를 인증 하 고 공유에 액세스할 수 있는 권한을 부여 해야 합니다. 이 작업은 파일 공유에 액세스 하는 사용자의 id를 기반으로 수행 됩니다. Azure Files는 세 가지 주요 id 공급자와 통합 됩니다.
- **온-프레미스 Active Directory Domain Services (AD DS 또는 온-프레미스 AD DS)**: Azure Storage 계정은 Windows Server 파일 서버 또는 NAS 장치와 마찬가지로 고객이 소유 하는 Active Directory Domain Services 도메인에 가입할 수 있습니다. 도메인 컨트롤러를 온-프레미스, Azure VM 또는 다른 클라우드 공급자의 VM으로 배포할 수 있습니다. Azure Files은 도메인 컨트롤러가 호스트 되는 위치와 무관 합니다. 저장소 계정이 도메인에 가입 되 면 최종 사용자는를 사용 하 여 PC에 로그인 한 사용자 계정을 사용 하 여 파일 공유를 탑재할 수 있습니다. AD 기반 인증은 Kerberos 인증 프로토콜을 사용 합니다.
- **Azure Active Directory Domain Services (azure AD DS)**: azure AD DS는 azure 리소스에 사용할 수 있는 Microsoft 관리 도메인 컨트롤러를 제공 합니다. Azure AD DS에 저장소 계정을 조인 하는 도메인은 고객이 소유한 Active Directory에 가입 하는 도메인에 비슷한 이점을 제공 합니다. 이 배포 옵션은 AD 기반 사용 권한이 필요한 응용 프로그램 리프트 및 시프트 시나리오에 가장 유용 합니다. Azure AD DS는 AD 기반 인증을 제공 하므로이 옵션은 또한 Kerberos 인증 프로토콜을 사용 합니다.
- Azure **storage 계정 키**: azure 저장소 계정 키를 사용 하 여 azure 파일 공유를 탑재할 수도 있습니다. 이러한 방식으로 파일 공유를 탑재 하기 위해 저장소 계정 이름이 사용자 이름으로 사용 되 고 저장소 계정 키가 암호로 사용 됩니다. 저장소 계정 키를 사용 하 여 Azure 파일 공유를 탑재 하는 작업은 실제로 관리자 작업입니다. 탑재 된 파일 공유에는 Acl이 있는 경우에도 공유의 모든 파일 및 폴더에 대 한 전체 권한이 있기 때문입니다. 저장소 계정 키를 사용 하 여 SMB를 통해 탑재 하는 경우 NTLMv2 인증 프로토콜이 사용 됩니다.

온-프레미스 파일 서버에서 마이그레이션하는 고객이 나 Windows 파일 서버 또는 NAS 어플라이언스와 같이 동작 하기 위해 Azure Files에서 새 파일 공유를 만드는 경우에는 저장소 계정을 **고객 소유 Active Directory** 에 가입 하는 것이 좋습니다. 스토리지 계정을 고객 소유의 Active Directory의 도메인에 조인하는 방법에 대한 자세한 내용은 [Azure Files Active Directory 개요](storage-files-active-directory-overview.md)를 참조하세요.

저장소 계정 키를 사용 하 여 Azure 파일 공유에 액세스 하려는 경우 [네트워킹](#networking) 섹션에 설명 된 대로 서비스 끝점을 사용 하는 것이 좋습니다.

## <a name="networking"></a>네트워킹
Azure 파일 공유는 저장소 계정의 공용 끝점을 통해 어디에서 나 액세스할 수 있습니다. 즉, 사용자의 로그온 ID로 권한이 부여된 요청과 같은 인증된 요청이 Azure 내부 또는 외부에서 안전하게 시작될 수 있습니다. 대부분의 고객 환경에서 Azure VM에서의 탑재가 성공하더라도 온-프레미스 워크스테이션에서 Azure 파일 공유의 초기 탑재가 실패합니다. 이는 많은 조직 및 ISP(인터넷 서비스 공급자)에서 SMB를 통해 통신하는 포트인 445 포트를 차단하기 때문입니다. 포트 445에서 시작되는 액세스를 허용하거나 거부하는 ISP에 대한 요약을 확인하려면 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)으로 이동합니다.

Azure 파일 공유에 대 한 액세스를 차단 해제 하기 위해 다음 두 가지 기본 옵션이 있습니다.

- 조직의 온-프레미스 네트워크에 대해 445 포트를 차단 해제 합니다. Azure 파일 공유는 SMB 3.0 및 FileREST API와 같은 인터넷 안전 프로토콜을 사용 하 여 공용 끝점을 통해서만 외부적으로 액세스할 수 있습니다. 이 방법은 조직의 아웃 바운드 포트 규칙을 변경 하는 것 외에도 고급 네트워킹 구성이 필요 하지 않으므로 온-프레미스에서 Azure 파일 공유에 액세스 하는 가장 쉬운 방법입니다. 그러나 smb 프로토콜의 레거시 및 사용 되지 않는 버전 (SMB 1.0)을 제거 하는 것이 좋습니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [windows/Windows Server 보안](storage-how-to-use-files-windows.md#securing-windowswindows-server) 설정 및 [Linux 보안](storage-how-to-use-files-linux.md#securing-linux)을 참조 하세요.

- Express 경로 또는 VPN 연결을 통해 Azure 파일 공유에 액세스 합니다. 네트워크 터널을 통해 Azure 파일 공유에 액세스 하는 경우 SMB 트래픽이 조직 경계를 통과 하지 않으므로 온-프레미스 파일 공유와 같은 Azure 파일 공유를 탑재할 수 있습니다.   

기술적 관점에서는 공용 끝점을 통해 Azure 파일 공유를 탑재 하는 것이 훨씬 더 간단 하지만 대부분의 고객은 Express 경로 또는 VPN 연결을 통해 Azure 파일 공유를 탑재 하도록 선택할 것으로 간주 합니다. 이러한 옵션을 탑재 하는 것은 SMB 및 NFS 공유 모두에서 가능 합니다. 이렇게 하려면 환경에 대해 다음을 구성 해야 합니다.  

- **Express 경로를 사용 하는 네트워크 터널링, 사이트 간 또는 지점 및 사이트 간 VPN**: 가상 네트워크로 터널링 하면 포트 445이 차단 된 경우에도 온-프레미스에서 Azure 파일 공유에 액세스할 수 있습니다.
- **개인 끝점**: 개인 끝점은 가상 네트워크의 주소 공간 내에서 저장소 계정에 전용 IP 주소를 제공 합니다. 이렇게 하면 Azure storage 클러스터에서 소유 하는 모든 IP 주소 범위에 대 한 온-프레미스 네트워크를 열 필요 없이 네트워크 터널링을 사용할 수 있습니다. 
- **DNS 전달**: 개인 끝점의 IP 주소로 확인할 수 있도록 온-프레미스 DNS를 구성 하 여 `storageaccount.file.core.windows.net` 공용 클라우드 지역에 대 한 저장소 계정의 이름을 확인 합니다.

Azure 파일 공유 배포와 관련 된 네트워킹을 계획 하려면 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조 하세요.

## <a name="encryption"></a>암호화
Azure Files는 두 가지 암호화 유형 (전송 중 암호화), Azure 파일 공유를 탑재/액세스할 때 사용 되는 암호화와 관련 된 암호화 및 저장소 데이터 암호화를 지원 합니다 .이는 디스크에 저장 될 때 데이터를 암호화 하는 방법과 관련이 있습니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화

> [!IMPORTANT]
> 이 섹션에서는 SMB 공유에 대한 전송 중 암호화 세부 정보에 대해 설명합니다. NFS 공유를 통한 전송 중 암호화에 대한 자세한 내용은 [보안](storage-files-compare-protocols.md#security)을 참조하세요.

기본적으로 모든 Azure 스토리지 계정은 전송 중 암호화를 사용하도록 설정되어 있습니다. 즉, SMB를 통해 파일 공유를 탑재하거나 FileREST 프로토콜(예: Azure Portal, PowerShell/CLI 또는 Azure SDK)을 통해 액세스할 때 Azure Files는 암호화 또는 HTTPS를 사용하는 SMB 3.0 이상을 통해 만든 연결만 허용합니다. SMB 3.0을 지원하지 않는 클라이언트 또는 SMB 3.0을 지원하지만 SMB 암호화를 지원하지 않는 클라이언트는 전송 중 암호화를 사용하도록 설정된 경우 Azure 파일 공유를 탑재할 수 없습니다. 암호화를 통해 SMB 3.0을 지원하는 운영 체제에 대한 자세한 내용은 자세한 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및 [Linux](storage-how-to-use-files-linux.md)용 설명서를 참조하세요. 모든 현재 버전의 PowerShell, CLI 및 SDK는 HTTPS를 지원합니다.  

Azure 스토리지 계정에 대해 전송 중 암호화를 사용하지 않도록 설정할 수 있습니다. 암호화를 사용 하지 않도록 설정 하면 Azure Files SMB 2.1, 암호화 되지 않은 SMB 3.0 및 HTTP를 통한 암호화 되지 않은 FileREST API 호출도 허용 됩니다. 전송 중 암호화를 사용하지 않도록 설정하는 주된 이유는 Windows Server 2008 R2 또는 이전 버전의 Linux 배포와 같은 이전 운영 체제에서 실행되어야 하는 레거시 애플리케이션을 지원하기 위해서입니다. Azure Files는 Azure 파일 공유와 동일한 Azure 지역 내에서만 SMB 2.1 연결을 허용합니다. Azure 파일 공유의 Azure 지역 외부(예: 온-프레미스 또는 다른 Azure 지역)에 있는 SMB 2.1 클라이언트는 파일 공유에 액세스할 수 없습니다.

전송 중 데이터 암호화를 사용하도록 설정하는 것이 좋습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>데이터 보호
Azure Files는 데이터를 백업, 복구 및 보안 위협 으로부터 보호할 수 있도록 하는 다중 계층 방식이 있습니다.

### <a name="soft-delete"></a>일시 삭제
파일 공유에 대 한 일시 삭제 (미리 보기)는 파일 공유를 실수로 삭제 한 경우 복구할 수 있도록 하는 저장소 계정 수준 설정입니다. 파일 공유를 삭제 하면 영구적으로 삭제 되는 대신 일시 삭제 된 상태로 전환 됩니다. 일시 삭제 된 데이터를 영구적으로 삭제 하기 전에 복구할 수 있는 시간을 구성 하 고,이 보존 기간 동안 언제 든 지 공유를 삭제 취소 합니다. 

대부분의 파일 공유에 대해 일시 삭제를 설정 하는 것이 좋습니다. 공유 삭제가 공통적이 고 예상 되는 워크플로가 있는 경우에는 보존 기간을 짧게 설정 하거나 일시 삭제를 사용 하도록 설정 하지 않을 수 있습니다.

일시 삭제에 대 한 자세한 내용은 [실수로 인 한 데이터 삭제 방지](./storage-files-prevent-file-share-deletion.md)를 참조 하세요.

### <a name="backup"></a>Backup
공유 [스냅숏](./storage-snapshots-files.md)(읽기 전용, 공유의 지정 시간 복사본)을 통해 Azure 파일 공유를 백업할 수 있습니다. 스냅숏은 증분 이므로 이전 스냅숏 이후 변경 된 데이터만 포함 합니다. 파일 공유 당 최대 200 개의 스냅숏을 보유 하 고 최대 10 년 동안 유지할 수 있습니다. PowerShell 또는 CLI (명령줄 인터페이스)를 통해 Azure Portal에서 수동으로 이러한 스냅숏을 만들거나 [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 사용할 수 있습니다. 스냅숏은 파일 공유 내에 저장 됩니다. 즉, 파일 공유를 삭제 하면 스냅숏은 삭제 됩니다. 스냅숏 백업이 실수로 삭제 되지 않도록 보호 하려면 공유에 대해 일시 삭제를 사용 하도록 설정 해야 합니다.

[Azure 파일 공유에 대 한 Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 는 스냅숏의 예약 및 보존을 처리 합니다. GFS (조부) 기능을 사용 하는 경우 매일, 매주, 매월 및 매년 스냅숏을 만들 수 있으며 각각 고유한 보존 기간을 갖습니다. 또한 오케스트레이션는 일시 삭제를 사용 하도록 설정 하 고 저장소 계정 내의 파일 공유가 백업에 대해 구성 되는 즉시 저장소 계정에 대 한 삭제 잠금을 수행 합니다. Azure Backup 마지막으로, Azure Backup은 고객이 백업 공간을 통합 하 여 볼 수 있도록 하는 특정 주요 모니터링 및 경고 기능을 제공 합니다.

Azure Backup를 사용 하 여 Azure Portal에서 항목 수준 및 공유 수준 복원을 모두 수행할 수 있습니다. 복원 지점 (특정 스냅숏), 특정 파일 또는 디렉터리 (관련 된 경우) 및 복원 하려는 위치 (원래 또는 대체)를 선택 하기만 하면 됩니다. 백업 서비스는 스냅숏 데이터 복사를 처리 하 고 포털의 복원 진행률을 보여 줍니다.

백업에 대 한 자세한 내용은 [Azure 파일 공유 백업](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)정보를 참조 하세요.

### <a name="azure-defender-for-azure-files"></a>Azure Files 용 Azure Defender 
Azure Defender for Azure Storage (이전에는 Azure Storage에 대 한 고급 위협 방지)는 저장소 계정에서 비정상적인 활동을 검색할 때 경고를 제공 하는 추가 보안 인텔리전스 계층을 제공 합니다 (예: 비정상적인 액세스 시도). 또한 맬웨어 해시 평판 분석을 실행 하 고 알려진 맬웨어에 대 한 경고를 생성 합니다. Azure Security Center를 통해 구독 또는 저장소 계정 수준에서 Azure Defender를 구성할 수 있습니다. 

자세한 내용은 [저장소에 대 한 Azure Defender 소개](../../security-center/defender-for-storage-introduction.md)를 참조 하세요.

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>표준 파일 공유를 최대 100TiB까지 확장하도록 설정
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>제한 사항
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>중복
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>마이그레이션
대부분의 경우에는 조직에 대 한 net new 파일 공유를 설정 하지 않고 기존 파일 공유를 온-프레미스 파일 서버 또는 NAS 장치에서 Azure Files로 마이그레이션해야 합니다. 시나리오에 적합 한 마이그레이션 전략 및 도구를 선택 하는 것은 마이그레이션의 성공에 중요 합니다. 

[마이그레이션 개요 문서](storage-files-migration-overview.md) 에서는 기본 사항에 대해 간략하게 설명 하 고 시나리오를 포괄 하는 마이그레이션 가이드를 안내 하는 표를 포함 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
* [Azure Files 배포](./storage-how-to-create-file-share.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
* [시나리오에 대 한 마이그레이션 가이드를 찾으려면 마이그레이션 개요 문서를 확인 하세요.](storage-files-migration-overview.md)