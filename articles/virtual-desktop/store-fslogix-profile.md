---
title: 저장소 FSLogix 프로필 컨테이너 Windows 가상 데스크톱-Azure
description: Azure Storage에 Windows 가상 데스크톱 FSLogix 프로필을 저장 하는 옵션입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
ms.openlocfilehash: e9410ce93d9e11d3023f4f461f3ba90a7bf74507
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451323"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows 가상 데스크톱의 FSLogix 프로필 컨테이너에 대 한 저장소 옵션

Azure는 FSLogix 프로필 컨테이너를 저장 하는 데 사용할 수 있는 여러 저장소 솔루션을 제공 합니다. 이 문서에서는 Azure에서 Windows 가상 데스크톱 FSLogix 사용자 프로필 컨테이너에 대해 제공 하는 저장소 솔루션을 비교 합니다.

Windows 가상 데스크톱은 FSLogix 프로필 컨테이너를 권장 사용자 프로필 솔루션으로 제공 합니다. FSLogix는 Windows 가상 데스크톱과 같은 원격 컴퓨팅 환경에서 프로필을 로밍 하도록 설계 되었습니다. 로그인 시이 컨테이너는 기본적으로 지원 되는 VHD (가상 하드 디스크) 및 Hyper-v VHDX (가상 하드 디스크)를 사용 하 여 컴퓨팅 환경에 동적으로 연결 됩니다. 사용자 프로필은 즉시 사용할 수 있으며 시스템에서 네이티브 사용자 프로필과 똑같이 표시 됩니다.

다음 표에서는 Windows 가상 데스크톱 FSLogix 프로필 컨테이너 사용자 프로필에 대 한 저장소 솔루션 Azure Storage 제공을 비교 합니다.

## <a name="azure-platform-details"></a>Azure 플랫폼 세부 정보

|기능|Azure Files|Azure NetApp Files|Storage Spaces Direct|
|--------|-----------|------------------|---------------------|
|플랫폼 서비스|예, Azure 네이티브 솔루션|예, Azure 네이티브 솔루션|아니요, 자체 관리|
|지역별 가용성|모든 지역|[영역 선택](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|모든 지역|
|중복성|로컬 중복/영역 중복/지역 중복|로컬 중복|로컬 중복/영역 중복/지역 중복|
|계층 및 성능|Standard<br>프리미엄<br>약 3 밀리초 대기 시간에 공유 당 5gbps와 공유 당 최대 10만 IOPS|Standard<br>프리미엄<br>매우<br>최대 320k (16K) IOPS (볼륨 당 4.5 GBps, 약 1 밀리초 대기 시간)|표준 HDD: 최대 500 IOPS 디스크당 제한<br>표준 SSD: 디스크당 최대 4k IOPS 제한<br>프리미엄 SSD: 디스크당 최대 20k IOPS 제한<br>스토리지 공간 다이렉트 프리미엄 디스크를 권장 합니다.|
|용량 중심|100 공유 당 TiB|볼륨당 100 TiB, 구독 당 최대 12.5 PiB|디스크당 최대 32 TiB|
|필수 인프라|최소 공유 크기 1 GiB|최소 용량 풀 4 TiB, 최소 볼륨 크기 100 GiB|Azure IaaS의 두 Vm (+ 클라우드 감시) 또는 디스크에 대 한 비용 없이 3 개 이상의 Vm|
|프로토콜|SMB 2.1/3. 및 REST|NFSv3, NFSv 4.1 (preview), SMB 3.x/2.x|NFSv3, NFSv 4.1, SMB 3.1|

## <a name="azure-management-details"></a>Azure 관리 세부 정보

|기능|Azure Files|Azure NetApp Files|Storage Spaces Direct|
|--------|-----------|------------------|---------------------|
|액세스|클라우드, 온-프레미스 및 하이브리드 (Azure 파일 동기화)|클라우드, 온-프레미스 (Express 경로를 통해)|클라우드, 온-프레미스|
|Backup|Azure backup 스냅숏 통합|Azure NetApp Files 스냅숏|Azure backup 스냅숏 통합|
|보안 및 규정 준수|[모든 Azure 지원 인증서](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 완료|[모든 Azure 지원 인증서](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 통합|Azure Active Directory 및 Azure Active Directory Domain Services|[Azure Active Directory Domain Services 및 네이티브 Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|네이티브 Active Directory 또는 Azure Active Directory Domain Services 지원만|

저장소 방법을 선택한 후 가격 계획에 대 한 자세한 내용은 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/) 을 확인 하세요.

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너, 사용자 프로필 디스크 및 기타 사용자 프로필 기술에 대해 자세히 알아보려면 [Fslogix 프로필 컨테이너 및 Azure files](fslogix-containers-azure-files.md)의 표를 참조 하세요.

사용자 고유의 FSLogix 프로필 컨테이너를 만들 준비가 되 면 다음 자습서 중 하나를 시작 하세요.

- [Windows 가상 데스크톱의 Azure Files에서 FSLogix 프로필 컨테이너 시작](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Azure NetApp 파일을 사용 하 여 호스트 풀의 FSLogix 프로필 컨테이너 만들기](create-fslogix-profile-container.md)
- [Azure에서 UPD 저장소에 대 한 2 노드 스토리지 공간 다이렉트 스케일 아웃 파일 서버 배포](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) 의 지침은 사용자 프로필 디스크 대신 FSLogix 프로필 컨테이너를 사용 하는 경우에도 적용 됩니다.

[Windows 가상 데스크톱에서 테넌트 만들기](tenant-setup-azure-active-directory.md)에서 처음부터 시작 하 여 고유한 Windows 가상 데스크톱 솔루션을 설정할 수도 있습니다.
