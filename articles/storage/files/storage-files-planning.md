---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포 계획을 이해 합니다. Azure 파일 공유를 직접 탑재 하거나 Azure File Sync를 사용 하 여 Azure 파일 공유를 온-프레미스로 캐시할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: db7ae0bd33bc52f80788db4994dcf2a3ca4d909a
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705914"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획
[Azure Files](storage-files-introduction.md) 는 서버를 사용 하지 않는 azure 파일 공유를 직접 탑재 하거나 Azure File Sync를 사용 하 여 온-프레미스에서 azure 파일 공유를 캐시 하는 두 가지 주요 방법으로 배포할 수 있습니다. 선택 하는 배포 옵션에 따라 배포를 계획할 때 고려해 야 할 사항이 변경 됩니다. 

- **Azure 파일 공유의 직접 탑재**: Azure Files는 SMB 액세스를 제공하므로 Windows, macOS 및 Linux에서 사용할 수 있는 표준 SMB 클라이언트를 사용하여 온-프레미스 또는 클라우드에서 Azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버리스이므로 프로덕션 시나리오를 위해 배포하는 경우 파일 서버 또는 NAS 디바이스를 관리할 필요가 없습니다. 즉 소프트웨어 패치를 적용하거나 실제 디스크를 교환할 필요가 없습니다. 

- **Azure 파일 동기화를 사용하여 온-프레미스에서 Azure 파일 공유 캐시**: Azure 파일 동기화를 사용하면 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 온-프레미스(또는 클라우드) Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. 

이 문서에서는 온-프레미스 또는 클라우드 클라이언트에서 직접 탑재할 Azure 파일 공유를 배포 하기 위한 배포 고려 사항을 주로 다룹니다. Azure File Sync 배포를 계획 하려면 [Azure File Sync 배포에 대 한 계획](storage-sync-files-planning.md)을 참조 하세요.

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

기술적 관점에서는 공용 끝점을 통해 Azure 파일 공유를 탑재 하는 것이 훨씬 더 간단 하지만 대부분의 고객은 Express 경로 또는 VPN 연결을 통해 Azure 파일 공유를 탑재 하도록 선택할 것으로 간주 합니다. 이렇게 하려면 환경에 대해 다음을 구성 해야 합니다.  

- **Express 경로를 사용 하는 네트워크 터널링, 사이트 간 또는 지점 및 사이트 간 VPN**: 가상 네트워크로 터널링 하면 포트 445이 차단 된 경우에도 온-프레미스에서 Azure 파일 공유에 액세스할 수 있습니다.
- **개인 끝점**: 개인 끝점은 가상 네트워크의 주소 공간 내에서 저장소 계정에 전용 IP 주소를 제공 합니다. 이렇게 하면 Azure storage 클러스터에서 소유 하는 모든 IP 주소 범위에 대 한 온-프레미스 네트워크를 열 필요 없이 네트워크 터널링을 사용할 수 있습니다. 
- **DNS 전달**: 개인 끝점의 IP 주소로 확인 되도록 온-프레미스 DNS를 구성 하 여 저장소 계정 (예: `storageaccount.file.core.windows.net` 공용 클라우드 지역)의 이름을 확인 합니다.

Azure 파일 공유 배포와 관련 된 네트워킹을 계획 하려면 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조 하세요.

## <a name="encryption"></a>암호화
Azure Files는 두 가지 암호화 유형 (전송 중 암호화), Azure 파일 공유를 탑재/액세스할 때 사용 되는 암호화와 관련 된 암호화 및 미사용 데이터 암호화를 지원 합니다 .이는 디스크에 저장 될 때 데이터를 암호화 하는 방법과 관련이 있습니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화
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

대부분의 파일 공유에 대해 일시 삭제를 설정 하는 것이 좋습니다. 공유 삭제가 공통적이 고 예상 되는 워크플로가 있는 경우 보존 기간을 매우 짧게 설정 하거나 일시 삭제를 사용 하도록 설정 하지 않을 수 있습니다.

일시 삭제에 대 한 자세한 내용은 [실수로 인 한 데이터 삭제 방지](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion)를 참조 하세요.

### <a name="backup"></a>Backup
공유 [스냅숏](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files)(읽기 전용, 공유의 지정 시간 복사본)을 통해 Azure 파일 공유를 백업할 수 있습니다. 스냅숏은 증분 이므로 이전 스냅숏 이후 변경 된 만큼의 데이터만 포함 합니다. 파일 공유 당 최대 200 개의 스냅숏을 보유 하 고 최대 10 년 동안 유지할 수 있습니다. PowerShell 또는 CLI (명령줄 인터페이스)를 통해 Azure Portal에서 수동으로 이러한 스냅숏을 만들거나 [Azure Backup](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json)를 사용할 수 있습니다. 스냅숏은 파일 공유 내에 저장 됩니다. 즉, 파일 공유를 삭제 하면 스냅숏은 삭제 됩니다. 스냅숏 백업이 실수로 삭제 되지 않도록 보호 하려면 공유에 대해 일시 삭제를 사용 하도록 설정 해야 합니다.

[Azure 파일 공유에 대 한 Azure Backup](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) 는 스냅숏의 예약 및 보존을 처리 합니다. GFS (조부) 기능을 사용 하는 경우 매일, 매주, 매월 및 매년 스냅숏을 만들 수 있으며 각각 고유한 보존 기간을 갖습니다. 또한 오케스트레이션는 일시 삭제를 사용 하도록 설정 하 고 저장소 계정 내의 파일 공유가 백업에 대해 구성 되는 즉시 저장소 계정에 대 한 삭제 잠금을 수행 합니다. Azure Backup 마지막으로, Azure Backup은 고객이 백업 공간을 통합 하 여 볼 수 있도록 하는 특정 주요 모니터링 및 경고 기능을 제공 합니다.

Azure Backup를 사용 하 여 Azure Portal에서 항목 수준 및 공유 수준 복원을 모두 수행할 수 있습니다. 복원 지점 (특정 스냅숏), 특정 파일 또는 디렉터리 (관련 된 경우) 및 복원 하려는 위치 (원래 또는 대체)를 선택 하기만 하면 됩니다. 백업 서비스는 스냅숏 데이터 복사를 처리 하 고 포털의 복원 진행률을 보여 줍니다.

백업에 대 한 자세한 내용은 [Azure 파일 공유 백업](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json)정보를 참조 하세요.

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Azure Files에 대 한 Advanced Threat Protection (미리 보기)
Azure Storage에 대 한 ATP (Advanced Threat Protection)는 저장소 계정에서 비정상적인 활동을 검색할 때 경고를 제공 하는 추가 보안 인텔리전스 계층을 제공 합니다 (예: 저장소 계정에 대 한 액세스를 비정상적인 시도). ATP는 맬웨어 해시 평판 분석도 실행 하 고 알려진 맬웨어에 대해 경고 합니다. Azure Security Center를 통해 구독 또는 저장소 계정 수준에서 ATP를 구성할 수 있습니다. 

자세한 내용은 [Azure Storage에 대 한 Advanced Threat protection](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)을 참조 하세요.

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

일반적으로 Azure Files 기능 및 다른 서비스와의 상호 운용성은 프리미엄 파일 공유와 표준 파일 공유 (트랜잭션 최적화, 핫 및 쿨 파일 공유 포함) 간에 동일 하지만 몇 가지 중요 한 차이점이 있습니다.
- **청구 모델**
    - 프리미엄 파일 공유는 프로 비전 된 청구 모델을 사용 하 여 청구 됩니다. 즉, 실제로 요청 하는 저장소 크기 보다 프로 비전 하는 저장소 용량에 대 한 비용을 지불 합니다. 
    - 표준 파일 공유에는 종 량 제 모델을 사용 하 여 요금이 청구 됩니다. 여기에는 실제로 소비 하는 저장소의 양에 대 한 기본 저장소 비용과 공유 사용 방법에 따라 추가 트랜잭션 비용이 포함 됩니다. 표준 파일 공유를 사용 하는 경우 Azure 파일 공유를 사용 (읽기/쓰기/탑재) 하면 청구서가 증가 합니다.
- **중복성 옵션**
    - 프리미엄 파일 공유는 LRS (로컬 중복) 및 ZRS (영역 중복) 저장소에만 사용할 수 있습니다.
    - 표준 파일 공유는 로컬 중복, 영역 중복, 지역 중복 (GRS) 및 GZRS (지역 영역 중복) 저장소에 사용할 수 있습니다.
- **파일 공유의 최대 크기**
    - 프리미엄 파일 공유는 추가 작업 없이 최대 100 TiB 프로 비전 할 수 있습니다.
    - 기본적으로 최대 5 개의 TiB만 사용할 수 있습니다. 단, 공유 제한은 *크게 파일 공유* 저장소 계정 기능 플래그를 옵트인 하 여 100 TiB로 늘릴 수 있습니다. 표준 파일 공유는 로컬 중복 또는 영역 중복 저장소 계정에 대해 최대 100 TiB 확장할 수 있습니다. 파일 공유 크기를 늘려야 하는 방법에 대 한 자세한 내용은 [large file 공유 사용 및 만들기](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)를 참조 하세요.
- **국가별 가용성**
    - 프리미엄 파일 공유는 모든 지역에서 사용할 수 없으며 영역 중복 지원은 더 작은 하위 지역에서 사용할 수 있습니다. 현재 지역에서 프리미엄 파일 공유를 사용할 수 있는지 확인 하려면 Azure에 대 한 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 페이지를 참조 하세요. ZRS를 지 원하는 지역을 확인 하려면 [지역별 Azure 가용성 영역 지원](../../availability-zones/az-region.md)을 참조 하세요. 새 지역 및 프리미엄 계층 기능의 우선 순위를 지정 하는 데 도움이 되도록이 [설문 조사](https://aka.ms/pfsfeedback)를 작성해 주세요.
    - 표준 파일 공유는 모든 Azure 지역에서 사용할 수 있습니다.
- AKS (Azure Kubernetes Service)는 1.13 이상 버전에서 프리미엄 파일 공유를 지원 합니다.

프리미엄 또는 표준 파일 공유로 파일 공유를 만든 후에는 자동으로 다른 계층으로 변환할 수 없습니다. 다른 계층으로 전환 하려는 경우 해당 계층에서 새 파일 공유를 만들고 원래 공유에서 만든 새 공유로 데이터를 수동으로 복사 해야 합니다. `robocopy`Windows 또는 macOS 및 Linux 용을 사용 하 여 해당 복사를 수행 하는 것이 좋습니다 `rsync` .

### <a name="understanding-provisioning-for-premium-file-shares"></a>프리미엄 파일 공유에 대 한 프로 비전 이해
프리미엄 파일 공유는 고정 GiB/IOPS/처리량 비율을 기준으로 프로비전됩니다. 공유에 프로비전되는 각 GiB당 IOPS 1개, 초당 0.1MiB의 처리량이 공유당 최대 한도까지 지급됩니다. 허용되는 최소 프로비저닝 용량은 100GiB(최소 IOPS/처리량)입니다.

최상의 노력 원칙에 따라 모든 공유는 60분 이상의 기간 동안(공유 크기별로 다름) 프로비전된 스토리지 용량(GiB)당 IOPS를 3개까지 버스트할 수 있습니다. 새 공유에는 프로비전된 용량을 기준으로 전체 버스트 크레딧이 초기 제공됩니다.

공유는 1 GiB 증분 단위로 프로 비전 되어야 합니다. 최소 크기는 100 GiB, 다음 크기는 101 GiB 등입니다.

> [!TIP]
> 기준 IOPS = 1 * 프로 비전 된 GiB. (최대 10만 IOPS).
>
> 버스트 제한 = 3 * 기준선 IOPS. (최대 10만 IOPS).
>
> 송신 율 = 60 MiB/s + 0.06 * 프로 비전 된 GiB
>
> 수신 율 = 40 MiB/s + 0.04 * 프로 비전 된 GiB

프로 비전 된 공유 크기는 공유 할당량에 의해 지정 됩니다. 공유 할당량은 언제 든 지 늘릴 수 있지만 마지막 증가 이후 24 시간 후에만 감소할 수 있습니다. 할당량 증가 없이 24 시간 동안 기다린 후에는 다시 늘릴 때까지 공유 할당량을 원하는 횟수 만큼 줄일 수 있습니다. IOPS/처리량 크기 변경은 크기가 변경 된 후 몇 분 내에 적용 됩니다.

사용 된 GiB에서 프로 비전 된 공유의 크기를 줄일 수 있습니다. 이 작업을 수행 하는 경우 데이터가 손실 되지 않지만 사용 된 크기에 대 한 요금이 청구 되며, 사용 되는 크기가 아니라 프로 비전 된 공유의 성능 (기준선 IOPS, 처리량 및 버스트 IOPS)을 수신 하 게 됩니다.

다음 표에서는 프로 비전 된 공유 크기에 대 한 이러한] 열의 공식을의 몇 가지 예를 보여 줍니다.

|용량 (GiB) | 기준 IOPS | 버스트 IOPS | 송신 (MiB/s) | 수신 (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 최대 300     | 66   | 44   |
|500         | 500     | 최대 1500   | 90   | 60   |
|1,024       | 1,024   | 최대 3072   | 122   | 81   |
|5,120       | 5,120   | 최대 15360  | 368   | 245   |
|10240      | 10240  | 최대 30720  | 675 | 450   |
|33792      | 33792  | 최대 10만 | 2088 | 1392   |
|51200      | 51200  | 최대 10만 | 3,132 | 2088   |
|102400     | 100,000 | 최대 10만 | 6204 | 4136   |

> [!NOTE]
> 파일 공유 성능에는 컴퓨터 네트워크 제한, 사용 가능한 네트워크 대역폭, IO 크기, 병렬 처리 등 많은 요인이 적용 됩니다. 예를 들어, KiB 읽기/쓰기 IO 크기가 8 인 내부 테스트를 기반으로 SMB를 통한 프리미엄 파일 공유에 연결 된 단일 Windows 가상 머신, *표준 F16s_v2*는 20K 읽기 Iops 및 15K 쓰기 iops를 달성할 수 있습니다. 512 MiB 읽기/쓰기 IO 크기를 사용 하는 경우 동일한 VM이 1.1 GiB/s 송신 및 370 MiB/s 수신 처리량을 달성할 수 있습니다. 최대 성능 확장을 얻으려면 부하를 여러 Vm에 분산 합니다. 몇 가지 일반적인 성능 문제 및 해결 방법에 대해서는 [문제 해결 가이드](storage-troubleshooting-files-performance.md) 를 참조 하세요.

#### <a name="bursting"></a>화
프리미엄 파일 공유는 IOPS를 3 배까지 버스트 할 수 있습니다. 버스트는 자동화 되며 신용 시스템을 기반으로 작동 합니다. 버스트는 최상의 노력을 기반으로 작동 하며 버스트 제한은 보장 되지 않습니다. 파일 공유는 제한 *까지* 버스트 될 수 있습니다.

크레딧은 파일 공유에 대 한 트래픽이 기준선 IOPS 미만이 될 때마다 버스트 버킷에 누적 됩니다. 예를 들어 100 GiB 공유에는 100 기준선 IOPS가 있습니다. 공유의 실제 트래픽이 1 초 간격으로 40 IOPS 인 경우 60 사용 하지 않는 IOPS는 버스트 버킷으로 제공한 됩니다. 이러한 크레딧은 나중에 작업이 기준선 IOPs를 초과 하는 경우에 사용 됩니다.

> [!TIP]
> 버스트 버킷의 크기 = 기준선 IOPS * 2 * 3600.

공유가 기준선 IOPS를 초과 하 고 버스트 버킷에 크레딧을 가질 때마다 버스트 됩니다. 크레딧이 남아 있는 한 공유는 지속 될 수 있지만, 50 TiB 보다 작은 공유는 최대 1 시간 동안 버스트 제한에만 유지 됩니다. 50 보다 큰 공유는 기술적으로 1 시간 제한 (최대 2 시간)을 TiB 수 있지만이는 발생 하는 버스트 크레딧 수를 기준으로 합니다. 기준 IOPS를 초과 하는 각 IO는 하나의 크레딧을 사용 하며, 모든 크레딧이 사용 되 면 공유는 기준선 IOPS로 돌아옵니다.

공유 크레딧에는 세 가지 상태가 있습니다.

- 발생 파일 공유에서 기준선 IOPS 보다 작은 값을 사용 하는 경우입니다.
- 파일 공유가 버스트 인 경우 거절
- 남은 상수 (크레딧을 사용 하지 않거나 기준선 IOPS가 사용 중인 경우)

새 파일 공유는 버스트 버킷의 전체 크레딧 수로 시작 합니다. 서버 제한으로 인해 공유 IOPS가 기준선 IOPS 미만이 면 버스트 크레딧을 계산 하지 않습니다.

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
* [Azure File Sync 배포 계획](storage-sync-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
* [시나리오에 대 한 마이그레이션 가이드를 찾으려면 마이그레이션 개요 문서를 확인 하세요.](storage-files-migration-overview.md)
