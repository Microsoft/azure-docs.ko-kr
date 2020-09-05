---
title: Azure Blob storage (미리 보기)의 네트워크 파일 시스템 3.0 지원 | Microsoft Docs
description: 이제 Blob storage는 NFS (네트워크 파일 시스템) 3.0 프로토콜을 지원 합니다. 이 지원을 통해 Windows 및 Linux 클라이언트는 Azure VM (가상 머신) 또는 온-프레미스를 실행 하는 컴퓨터에서 Blob storage에 컨테이너를 탑재할 수 있습니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 41ad20de6b7a800ff1f97a7ff371c8e0012fed27
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166980"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob storage에서 NFS (네트워크 파일 시스템) 3.0 프로토콜 지원 (미리 보기)

이제 Blob storage는 NFS (네트워크 파일 시스템) 3.0 프로토콜을 지원 합니다. 이 지원을 통해 Windows 또는 Linux 클라이언트는 Azure VM (가상 머신) 또는 온-프레미스 컴퓨터에서 Blob 저장소에 컨테이너를 탑재할 수 있습니다. 

> [!NOTE]
> NFS 3.0 Azure Blob storage의 프로토콜 지원은 공개 미리 보기로 제공 되며 미국 동부, 미국 중부, 미국 서 부 중부, 오스트레일리아 남동쪽, 서유럽, 영국 서부, 대한민국 중부, 한국 남부 및 캐나다 중부 지역에서 사용할 수 있습니다.

## <a name="general-workflow-mounting-a-storage-account-container"></a>일반 워크플로: 저장소 계정 컨테이너 탑재

저장소 계정 컨테이너를 탑재 하려면 다음 작업을 수행 해야 합니다.

1. 구독과 함께 NFS 3.0 프로토콜 기능을 등록 합니다.

2. 기능이 등록 되어 있는지 확인 합니다.

3. Azure Virtual Network (VNet)를 만듭니다.

4. 네트워크 보안을 구성 합니다.

5. VNet의 트래픽만 허용 하는 저장소 계정을 만들고 구성 합니다.

6. 저장소 계정에서 컨테이너를 만듭니다.

7. 컨테이너를 탑재 합니다.

단계별 지침은 [NFS (네트워크 파일 시스템) 3.0 프로토콜을 사용 하 여 Blob Storage 탑재 (미리 보기)](network-file-system-protocol-support-how-to.md)를 참조 하세요.

> [!IMPORTANT]
> 이러한 작업을 순서 대로 완료 하는 것이 중요 합니다. 계정에서 NFS 3.0 프로토콜을 사용 하도록 설정 하기 전에 만든 컨테이너를 탑재할 수 없습니다. 또한 계정에서 NFS 3.0 프로토콜을 사용 하도록 설정한 후에는 사용 하지 않도록 설정할 수 없습니다.

## <a name="network-security"></a>네트워크 보안

저장소 계정은 VNet 내에 포함 되어야 합니다. VNet을 통해 클라이언트는 저장소 계정에 안전 하 게 연결할 수 있습니다. 계정에서 데이터를 보호 하는 유일한 방법은 VNet 및 기타 네트워크 보안 설정을 사용 하는 것입니다. 계정 키 인증, AD (Azure Active Directory) 보안 및 Acl (액세스 제어 목록)을 포함 하 여 데이터를 보호 하는 데 사용 되는 다른 도구는 NFS 3.0 프로토콜 지원이 설정 된 계정에서 아직 지원 되지 않습니다. 

자세히 알아보려면 [Blob 저장소에 대 한 네트워크 보안 권장 사항](security-recommendations.md#networking)을 참조 하세요.

## <a name="supported-network-connections"></a>지원 되는 네트워크 연결

클라이언트는 공용 또는 [개인 끝점](../common/storage-private-endpoints.md)을 통해 연결할 수 있으며 다음 네트워크 위치 중 하나에서 연결할 수 있습니다.

- 저장소 계정에 대해 구성 하는 VNet입니다. 

  이 문서에서는 VNet을 *기본 vnet*으로 지칭 합니다. 자세히 알아보려면 [가상 네트워크에서 액세스 권한 부여](../common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조 하세요.

- 기본 VNet과 동일한 지역에 있는 피어 링 VNet입니다.

  이 피어 링 VNet에 대 한 액세스를 허용 하도록 저장소 계정을 구성 해야 합니다. 자세히 알아보려면 [가상 네트워크에서 액세스 권한 부여](../common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조 하세요.

- [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [express 경로 게이트웨이](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)를 사용 하 여 기본 VNet에 연결 된 온-프레미스 네트워크입니다. 

  자세히 알아보려면 [온-프레미스 네트워크에서 액세스 구성](../common/storage-network-security.md#configuring-access-from-on-premises-networks)을 참조 하세요.

- 피어 링 네트워크에 연결 된 온-프레미스 네트워크

  [게이트웨이 전송](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)과 함께 [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [express 경로 게이트웨이](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) 를 사용 하 여이 작업을 수행할 수 있습니다. 

> [!IMPORTANT]
> 온-프레미스 네트워크에서 연결 하는 경우 클라이언트가 포트 111 및 2048를 통해 나가는 통신을 허용 하는지 확인 합니다. NFS 3.0 프로토콜은 이러한 포트를 사용 합니다.

## <a name="azure-storage-features-not-yet-supported"></a>아직 지원 되지 않는 Azure Storage 기능

계정에서 NFS 3.0 프로토콜을 사용 하도록 설정 하는 경우 다음 Azure Storage 기능이 지원 되지 않습니다. 

- AD (Azure Active Directory) 보안

- POSIX와 유사한 Acl (access control 목록)

- 기존 저장소 계정에서 NFS 3.0 지원을 사용 하도록 설정 하는 기능

- 저장소 계정에서 NFS 3.0 지원을 사용 하지 않도록 설정 하는 기능 (설정 후)

- REST Api 또는 Sdk를 사용 하 여 blob에 쓸 수 있습니다. 
  
## <a name="nfs-30-features-not-yet-supported"></a>NFS 3.0 기능이 아직 지원 되지 않음

다음 NFS 3.0 기능은 Azure Data Lake Storage Gen2에서 아직 지원 되지 않습니다.

- UDP를 통한 NFS 3.0. TCP를 통한 NFS 3.0만 지원 됩니다.

- NLM (네트워크 잠금 관리자)를 사용 하 여 파일을 잠급니다. 탑재 명령은 매개 변수를 포함 해야 합니다 `-o nolock` .

- 하위 디렉터리를 탑재 합니다. 루트 디렉터리 (컨테이너)만 탑재할 수 있습니다.

- 탑재 목록 (예: 명령 사용 `showmount -a` )

- 내보내기 나열 (예: 명령 사용 `showmount -e` )

- 컨테이너를 읽기 전용으로 내보내기

## <a name="pricing"></a>가격 책정

미리 보기 중에 저장소 계정에 저장 된 데이터는 월별 GB 당 blob 저장소 요금이 청구 되는 것과 동일한 용량 속도로 청구 됩니다. 

미리 보기 동안에는 트랜잭션이 청구 되지 않습니다. 트랜잭션에 대 한 가격은 변경 될 수 있으며 일반 공급 될 때 결정 됩니다.

## <a name="next-steps"></a>다음 단계

시작 하려면 [NFS (네트워크 파일 시스템) 3.0 프로토콜을 사용 하 여 Blob 저장소 탑재 (미리 보기)](network-file-system-protocol-support-how-to.md)를 참조 하세요.





