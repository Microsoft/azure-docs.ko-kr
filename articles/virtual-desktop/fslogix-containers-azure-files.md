---
title: Windows 가상 데스크톱 FSLogix 프로필 컨테이너 파일-Azure
description: 이 문서에서는 Windows 가상 데스크톱 및 Azure files 내의 FSLogix 프로필 컨테이너에 대해 설명 합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: d45fa08383d9f61776a739093d78fc033ad54a6b
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212405"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 프로필 컨테이너 및 Azure Files

Windows 가상 데스크톱 서비스는 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 권장 합니다. FSLogix는 Windows 가상 데스크톱과 같은 원격 컴퓨팅 환경에서 프로필을 로밍 하도록 설계 되었습니다. 단일 컨테이너에 전체 사용자 프로필을 저장 합니다. 로그인 시이 컨테이너는 기본적으로 지원 되는 VHD (가상 하드 디스크) 및 Hyper-v VHDX (가상 하드 디스크)를 사용 하 여 컴퓨팅 환경에 동적으로 연결 됩니다. 사용자 프로필은 즉시 사용할 수 있으며 시스템에서 네이티브 사용자 프로필과 똑같이 표시 됩니다. 이 문서에서는 Windows 가상 데스크톱에서 Azure Files 함수를 사용 하 여 FSLogix 프로필 컨테이너를 사용 하는 방법을 설명 합니다.

>[!NOTE]
>Azure의 여러 FSLogix 프로필 컨테이너 저장소 옵션에 대 한 비교 자료를 찾고 있는 경우 [FSLogix 프로필 컨테이너에 대 한 저장소 옵션](store-fslogix-profile.md)을 참조 하세요.

## <a name="user-profiles"></a>사용자 프로필

사용자 프로필에는 데스크톱 설정, 영구 네트워크 연결 및 응용 프로그램 설정과 같은 구성 정보를 포함 하 여 개별에 대 한 데이터 요소가 포함 됩니다. 기본적으로 Windows는 운영 체제와 긴밀 하 게 통합 되는 로컬 사용자 프로필을 만듭니다.

원격 사용자 프로필은 사용자 데이터와 운영 체제 간의 파티션을 제공 합니다. 이를 통해 사용자 데이터에 영향을 주지 않고 운영 체제를 바꾸거나 변경할 수 있습니다. RDSH (원격 데스크톱 세션 호스트) 및 VDI (가상 데스크톱 인프라)에서 다음과 같은 이유로 운영 체제를 대체할 수 있습니다.

- 운영 체제 업그레이드
- 기존 VM (가상 컴퓨터) 대체
- 풀링된 (비영구) RDSH 또는 VDI 환경에 속한 사용자

Microsoft 제품은 다음과 같은 기술을 포함 하 여 원격 사용자 프로필에 대 한 몇 가지 기술로 운영 됩니다.
- 로밍 사용자 프로필 (RUP)
- 사용자 프로필 디스크 (UPD)
- ESR (엔터프라이즈 상태 로밍)

UPD 및 RUP는 RDSH (원격 데스크톱 세션 호스트) 및 VHD (가상 하드 디스크) 환경에서 사용자 프로필에 대해 가장 널리 사용 되는 기술입니다.

### <a name="challenges-with-previous-user-profile-technologies"></a>이전 사용자 프로필 기술과 관련 된 문제

사용자 프로필에 대 한 기존 및 레거시 Microsoft 솔루션에는 다양 한 과제가 제공 됩니다. 이전 솔루션이 RDSH 또는 VDI 환경과 함께 제공 되는 모든 사용자 프로필 요구를 처리 하지 않았습니다. 예를 들어 UPD는 많은 OST 파일을 처리할 수 없으며 RUP는 최신 설정을 유지 하지 않습니다.

#### <a name="functionality"></a>기능

다음 표에서는 이전 사용자 프로필 기술의 이점과 제한 사항을 보여 줍니다.

| 기술 | 최신 설정 | Win32 설정 | OS 설정 | 사용자 데이터 | 서버 SKU에서 지원 됨 | Azure의 백 엔드 저장소 | 백 엔드 저장소 온-프레미스 | 버전 지원 | 후속 로그인 시간 |메모|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **사용자 프로필 디스크 (UPD)** | yes | yes | yes | yes | yes | 예 | yes | Win 7 이상 | yes | |
| **로밍 사용자 프로필 (RUP), 유지 관리 모드** | 예 | yes | yes | yes | yes| 예 | yes | Win 7 이상 | 예 | |
| **Enterprise State Roaming (ESR)** | yes | 예 | yes | 예 | 참고 사항 보기 | yes | 예 | Win 10 | 예 | 서버 SKU에서 함수를 사용 하지만 지원 사용자 인터페이스를 지원 하지 않습니다. |
| **UE-V (사용자 환경 가상화)** | yes | yes | yes | 예 | yes | 예 | yes | Win 7 이상 | 예 |  |
| **OneDrive 클라우드 파일** | 예 | 예 | 예 | yes | 참고 사항 보기 | 참고 사항 보기  | 참고 참조 | Win 10 RS3 | 예 | 서버 SKU에서 테스트 되지 않았습니다. Azure의 백 엔드 저장소는 동기화 클라이언트에 따라 달라 집니다. 백 엔드 저장소 온-프레미스에 동기화 클라이언트가 필요 합니다. |

#### <a name="performance"></a>성능

성능 요구 사항을 해결 하려면 UPD [(S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) 를 사용 스토리지 공간 다이렉트 해야 합니다. UPD는 SMB (서버 메시지 블록) 프로토콜을 사용 합니다. 사용자가 기록 되는 VM에 프로필을 복사 합니다. S2D를 사용 하는 UPD Windows 가상 데스크톱에 권장 되는 솔루션입니다.  

#### <a name="cost"></a>비용

S2D 클러스터는 필요한 성능을 구현 하지만 기업 고객의 경우 비용이 많이 듭니다. 특히 중소기업 (중소기업) 고객의 경우 비용이 많이 듭니다. 이 솔루션의 경우 기업은 저장소 디스크에 대해 요금을 지불 하 고 공유에 대 한 디스크를 사용 하는 Vm의 비용을 지불 합니다.

#### <a name="administrative-overhead"></a>관리 오버 헤드

S2D 클러스터에는 보안 상태에서 패치, 업데이트 및 유지 관리 되는 운영 체제가 필요 합니다. 이러한 프로세스와 S2D 재해 복구를 설정 하는 복잡성은 전용 IT 직원이 있는 기업에 대해서만 S2D를 사용할 수 있도록 합니다.

## <a name="fslogix-profile-containers"></a>FSLogix 프로필 컨테이너

2018 년 11 월 19 일에 [Microsoft에서 FSLogix를 얻었습니다](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix는 많은 프로필 컨테이너 문제를 해결 합니다. 키의 핵심은 다음과 같습니다.

- **성능:** [Fslogix 프로필 컨테이너](/fslogix/configure-profile-container-tutorial/) 는 고성능 이며 캐시 된 exchange 모드를 차단 하는 성능 문제를 해결 합니다.
- **OneDrive:** FSLogix 프로필 컨테이너가 없으면 비영구 RDSH 또는 VDI 환경에서 비즈니스용 OneDrive가 지원 되지 않습니다. [비즈니스용 OneDrive 및 FSLogix 모범 사례에서](/fslogix/overview/) 상호 작용 하는 방법을 설명 합니다. 자세한 내용은 [가상 데스크톱에서 동기화 클라이언트 사용](/deployoffice/rds-onedrive-business-vdi/)을 참조 하세요.
- **추가 폴더:** FSLogix는 추가 폴더를 포함 하도록 사용자 프로필을 확장 하는 기능을 제공 합니다.

취득 한 후 Microsoft는 UPD와 같은 기존 사용자 프로필 솔루션을 FSLogix 프로필 컨테이너와 바꾸기 시작 했습니다.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files Azure Active Directory 도메인 서비스와 통합

FSLogix 프로필 컨테이너의 성능 및 기능은 클라우드를 활용 합니다. 2019 7 월 7 일에 Microsoft Azure 파일은 [AD DS (Azure Active Directory 도메인 서비스)를 사용 하 여 Azure Files 인증](../storage/files/storage-files-active-directory-overview.md)의 일반 공급을 발표 했습니다. 비용 및 관리 오버 헤드를 모두 해결 함으로써 Azure AD DS 인증을 사용 하는 Azure Files Windows 가상 데스크톱 서비스의 사용자 프로필에 대 한 프리미엄 솔루션입니다.

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 모범 사례

Windows 가상 데스크톱은 고객이 사용 중인 Vm의 크기, 유형 및 수에 대 한 모든 권한을 제공 합니다. 자세한 내용은 [Windows 가상 데스크톱 이란?](overview.md)을 참조 하세요.

Windows 가상 데스크톱 환경이 모범 사례를 따르는지 확인 하려면 다음을 수행 합니다.

- Azure Files 저장소 계정은 세션 호스트 Vm과 동일한 지역에 있어야 합니다.
- Azure Files 권한은 [요구 사항-프로필 컨테이너](/fslogix/overview#requirements/)에 설명 된 권한과 일치 해야 합니다.
- 각 호스트 풀은 동일한 마스터 이미지를 기반으로 동일한 유형 및 크기 VM을 기반으로 빌드해야 합니다.
- 관리, 크기 조정 및 업데이트를 지원 하려면 각 호스트 풀 VM이 동일한 리소스 그룹에 있어야 합니다.
- 성능을 최적화 하려면 저장소 솔루션과 FSLogix 프로필 컨테이너가 동일한 데이터 센터 위치에 있어야 합니다.
- 마스터 이미지를 포함 하는 저장소 계정은 Vm이 프로 비전 되는 동일한 지역 및 구독에 있어야 합니다.

## <a name="next-steps"></a>다음 단계

다음 가이드를 사용 하 여 Windows 가상 데스크톱 환경을 설정 합니다.

- 데스크톱 가상화 솔루션 빌드를 시작 하려면 [Windows 가상 데스크톱에서 테 넌 트 만들기](tenant-setup-azure-active-directory.md)를 참조 하세요.
- Windows 가상 데스크톱 테 넌 트 내에서 호스트 풀을 만들려면 [Azure Marketplace를 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)를 참조 하세요.
- 클라우드에서 완전히 관리 되는 파일 공유를 설정 하려면 [Azure Files 공유 설정](/azure/storage/files/storage-files-active-directory-enable/)을 참조 하세요.
- FSLogix 프로필 컨테이너를 구성 하려면 [파일 공유를 사용 하 여 호스트 풀에 대 한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)를 참조 하세요.
- 호스트 풀에 사용자를 할당 하려면 [Windows 가상 데스크톱에 대 한 앱 그룹 관리](manage-app-groups.md)를 참조 하세요.
- 웹 브라우저에서 Windows 가상 데스크톱 리소스에 액세스 하려면 [Windows 가상 데스크톱에 연결](connect-web.md)을 참조 하세요.
