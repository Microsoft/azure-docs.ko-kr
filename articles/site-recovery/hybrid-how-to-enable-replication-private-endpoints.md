---
title: 전용 끝점을 사용 하 여 온-프레미스 컴퓨터에 대 한 복제 사용
description: 이 문서에서는 Site Recovery에서 개인 끝점을 사용 하 여 온-프레미스 컴퓨터에 대 한 복제를 구성 하는 방법을 설명 합니다.
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 13c19f07ac21f986a5523407e46c59c050ebf96d
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142080"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>개인 끝점을 사용 하 여 온-프레미스 컴퓨터 복제

Azure Site Recovery를 사용 하면 azure [개인 링크](../private-link/private-endpoint-overview.md) 개인 끝점을 사용 하 여 온-프레미스 컴퓨터를 azure의 가상 네트워크에 복제할 수 있습니다. 복구 자격 증명 모음에 대 한 개인 끝점 액세스 지원은 다음 지역에서 지원 됩니다.

- Azure 상용: 미국 동부, 미국 서 부 2, 미국 동부
- Azure Government: US Gov 버지니아, US Gov 애리조나, US Gov 텍사스, US DoD 동부, US DoD 중부

이 항목에서는 다음 단계를 수행하는 방법에 대해 설명합니다.

- 컴퓨터를 보호 하기 위해 Azure Backup Recovery Services 자격 증명 모음을 만듭니다.
- 자격 증명 모음에 관리 되는 id를 사용 하도록 설정 합니다. 온-프레미스에서 Azure 대상 위치로의 트래픽을 복제할 수 있도록 저장소 계정에 액세스 하는 데 필요한 권한을 부여 합니다. 저장소에 대 한 관리 id 액세스는 자격 증명 모음에 대 한 개인 링크 액세스를 위해 필요 합니다.

- 전용 끝점에 필요한 DNS 변경을 수행 합니다.
- 가상 네트워크 내에서 자격 증명 모음에 대 한 개인 끝점을 만들고 승인 합니다.
- 저장소 계정에 대 한 개인 끝점을 만듭니다. 필요에 따라 저장소에 대 한 공용 또는 방화벽이 사용 액세스를 계속 허용할 수 있습니다. Azure Site Recovery에는 저장소에 액세스 하기 위한 개인 끝점을 만들 필요가 없습니다.
  
다음 다이어그램에서는 개인 끝점을 사용한 하이브리드 재해 복구에 대 한 복제 워크플로를 보여 줍니다. 온-프레미스 네트워크에 개인 끝점을 만들 수 없습니다. 개인 링크를 사용 하려면 Azure 가상 네트워크 (이 문서에서는 *바이패스 네트워크* 라고 함)를 만들고, 온-프레미스와 바이패스 네트워크 간에 개인 연결을 설정한 다음 바이패스 네트워크에서 개인 끝점을 만들어야 합니다. 모든 형태의 개인 연결을 선택할 수 있습니다.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Azure Site Recovery 및 전용 끝점의 아키텍처를 보여 주는 다이어그램입니다.":::

## <a name="prerequisites-and-caveats"></a>필수 조건 및 주의 사항

- 개인 링크는 Site Recovery 9.35 이상에서 지원 됩니다.
- 새 Recovery Services 자격 증명 모음에 등록 된 항목이 없는 새 자격 증명 모음에 대해서만 개인 끝점을 만들 수 있습니다. 따라서 자격 증명 모음에 항목을 추가 하기 전에 개인 끝점을 만들어야 합니다. 가격 책정 정보는 [Azure 개인 링크 가격 책정](https://azure.microsoft.com/pricing/details/private-link/) 을 참조 하세요.
- 자격 증명 모음에 대 한 개인 끝점을 만들 때 자격 증명 모음이 잠깁니다. 개인 끝점이 있는 네트워크 에서만 액세스할 수 있습니다.
- Azure Active Directory는 현재 개인 끝점을 지원 하지 않습니다. 따라서 보안 Azure 가상 네트워크에서 Ip로의 아웃 바운드 액세스를 허용 하 고, 지역에서 Azure Active Directory 하는 데 필요한 정규화 된 도메인 이름에 대 한 아웃 바운드 액세스를 허용 해야 합니다.
  해당 하는 경우 네트워크 보안 그룹 태그 "Azure Active Directory" 및 Azure 방화벽 태그를 사용 하 여 Azure Active Directory에 대 한 액세스를 허용할 수도 있습니다.
- 개인 끝점을 만드는 바이패스 네트워크에 5 개의 IP 주소가 필요 합니다. 자격 증명 모음에 대 한 개인 끝점을 만들 때 Site Recovery는 마이크로 서비스에 액세스 하기 위해 5 개의 개인 링크를 만듭니다.
- 캐시 저장소 계정에 대 한 개인 끝점 연결에 대 한 네트워크 바이패스 네트워크에서 하나의 추가 IP 주소가 필요 합니다. 온-프레미스와 저장소 계정 끝점 간의 모든 연결 방법을 사용할 수 있습니다. 예를 들어 인터넷 또는 Azure [express](../expressroute/index.yml)경로를 사용할 수 있습니다. 개인 링크 설정은 선택 사항입니다. 범용 v2 계정 에서만 저장소에 대 한 개인 끝점을 만들 수 있습니다. 범용 v2 계정에서의 데이터 전송 가격 책정에 대 한 자세한 내용은 [Azure 페이지 blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/page-blobs/) 을 참조 하세요.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>사이트 복구를 위한 개인 끝점 만들기 및 사용

 다음 섹션에서는 가상 네트워크에서 site recovery에 대 한 개인 끝점을 만들고 사용 하기 위해 수행 해야 하는 단계에 대해 설명 합니다.

> [!NOTE]
> 이러한 단계를 표시 된 순서 대로 따르는 것이 좋습니다. 그렇지 않으면 자격 증명 모음에서 개인 끝점을 사용 하지 못할 수 있으며 새 자격 증명 모음을 사용 하 여 프로세스를 다시 시작 해야 할 수 있습니다.

### <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음에는 컴퓨터의 복제 정보가 포함 됩니다. Site Recovery 작업을 트리거하는 데 사용 됩니다. 재해가 발생 한 경우 장애 조치 (failover) 하려는 Azure 지역에서 Recovery Services 자격 증명 모음을 만드는 방법에 대 한 자세한 내용은 [Recovery Services 자격 증명 모음 만들기](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)를 참조 하세요.

### <a name="enable-the-managed-identity-for-the-vault"></a>자격 증명 모음에 관리 되는 id 사용

[관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하면 자격 증명 모음에서 저장소 계정에 액세스할 수 있습니다. 사용자의 요구 사항에 따라 대상 저장소 및 캐시/로그 저장소 계정에 액세스 해야 하는 Site Recovery 있습니다. 관리 id 액세스는 자격 증명 모음에 개인 링크 서비스를 사용 하는 경우에 필요 합니다.

1. Recovery Services 자격 증명 모음으로 이동 합니다. **설정**아래에서 **id** 를 선택 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Id 설정 페이지를 보여 주는 스크린샷":::

1. **상태** 를 **켜기** 로 변경 하 고 **저장**을 선택 합니다.

   개체 ID가 생성 됩니다. 이제 자격 증명 모음이 Azure Active Directory 등록 됩니다.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 대 한 개인 끝점 만들기

온-프레미스 원본 네트워크의 컴퓨터를 보호 하려면 바이패스 네트워크에서 자격 증명 모음에 대해 하나의 개인 끝점을 사용 해야 합니다. Azure Portal 또는 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)를 사용 하 여 개인 링크 센터를 사용 하 여 개인 끝점을 만듭니다.

1. Azure Portal 검색 상자에서 "개인 링크"를 검색 합니다. 비공개 **링크를 선택 하** 여 개인 링크 센터로 이동 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="개인 링크 센터에 대 한 Azure Portal 검색을 보여 주는 스크린샷":::

1. 왼쪽 창에서 **개인 끝점**을 선택 합니다. **개인 끝점** 페이지에서 **추가** 를 선택 하 여 자격 증명 모음에 대 한 개인 끝점 만들기를 시작 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="개인 링크 센터에서 개인 끝점을 만드는 방법을 보여 주는 스크린샷":::

1. **개인 끝점 만들기** 페이지에서 세부 정보를 지정 하 여 개인 끝점 연결을 만듭니다.

   1. **기본 사항**. 개인 끝점에 대 한 기본 세부 정보를 제공 합니다. 바이패스 네트워크에 사용한 지역을 사용 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="개인 끝점을 만들기 위한 기본 탭을 보여 주는 스크린샷":::

   1. **리소스**. 이 탭에서 연결을 만들 platform.object (platform as a service) 리소스를 지정 해야 합니다. 선택한 구독에 대 한 **리소스 유형** 에서 **Microsoft recoveryservices/자격 증명 모음**을 선택 합니다. **리소스**에서 Recovery Services 자격 증명 모음의 이름을 선택 합니다. **대상 하위 리소스로** **Azure Site Recovery** 를 선택 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="전용 끝점에 연결 하기 위한 리소스 탭을 보여 주는 스크린샷":::

   1. **구성**. 이 탭에서 개인 끝점을 만들려는 바이패스 네트워크 및 서브넷을 지정 합니다. 

      **예**를 선택 하 여 개인 DNS 영역과의 통합을 사용 하도록 설정 합니다.
      기존 DNS 영역을 선택 하거나 새 DNS 영역을 만듭니다. **예** 를 선택 하면 자동으로 영역을 바이패스 네트워크에 연결 합니다. 이 작업은 또한 새 Ip의 DNS 확인에 필요한 DNS 레코드와 개인 끝점에 대해 만든 정규화 된 도메인 이름을 추가 합니다.

      동일한 자격 증명 모음에 연결 하는 모든 새 개인 끝점에 대 한 새 DNS 영역을 만들도록 선택 합니다. 기존 개인 DNS 영역을 선택 하는 경우 이전 CNAME 레코드를 덮어씁니다. 계속 하기 전에 [개인 끝점 지침](../private-link/private-endpoint-overview.md#private-endpoint-properties) 을 참조 하십시오.

      환경에 허브 및 스포크 모델이 있는 경우 하나의 개인 끝점 및 전체 설치에 대 한 하나의 개인 DNS 영역만 필요 합니다. 모든 가상 네트워크는 이미 피어 링을 사용 하도록 설정 되어 있기 때문입니다. 자세한 내용은 [개인 끝점 DNS 통합](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)을 참조 하세요.

      개인 DNS 영역을 수동으로 만들려면 [개인 dns 영역 만들기 및 수동으로 DNS 레코드 추가](#create-private-dns-zones-and-add-dns-records-manually)의 단계를 따르세요.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="개인 끝점의 구성에 대 한 구성 탭을 보여 주는 스크린샷":::

   1. **태그**. 필요에 따라 개인 끝점에 대 한 태그를 추가할 수 있습니다.

   1. ** \+ 만들기를 검토**합니다. 유효성 검사가 완료 되 면 **만들기** 를 선택 하 여 개인 끝점을 만듭니다.

개인 끝점을 만들 때 5 개의 정규화 된 도메인 이름 (Fqdn)이 개인 끝점에 추가 됩니다. 이러한 링크를 통해 온-프레미스 네트워크의 컴퓨터는 자격 증명 모음에서 필요한 모든 Site Recovery 마이크로 서비스를 사용 하 여 바이패스 네트워크를 통해 액세스할 수 있습니다. 우회 네트워크 및 모든 피어 링 네트워크에서 모든 Azure 컴퓨터의 보호를 위해 동일한 개인 끝점을 사용할 수 있습니다.

5 개의 도메인 이름은 다음 패턴으로 포맷 됩니다.

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>사이트 복구를 위한 개인 끝점 승인

개인 끝점을 만들 때 Recovery Services 자격 증명 모음의 소유자 이기도 한 경우 이전에 만든 개인 끝점은 몇 분 내에 자동으로 승인 됩니다. 그렇지 않으면 자격 증명 모음 소유자가 개인 끝점을 승인 해야 사용할 수 있습니다. 요청 된 개인 끝점 연결을 승인 하거나 거부 하려면 복구 자격 증명 모음 페이지의 **설정** 에서 **개인 끝점 연결** 로 이동 합니다.

계속 하기 전에 개인 끝점 리소스로 이동 하 여 연결 상태를 검토할 수 있습니다.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="자격 증명 모음의 개인 끝점 연결 페이지와 연결 목록을 보여 주는 스크린샷":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>필드 캐시 저장소 계정에 대 한 개인 끝점 만들기

개인 끝점을 사용 하 여 Azure Storage 수 있습니다. Azure Site Recovery 복제에 대 한 저장소 액세스를 위한 개인 끝점 만들기는 선택 사항입니다. 저장소에 대 한 개인 끝점을 만드는 경우 바이패스 가상 네트워크의 캐시/로그 저장소 계정에 대 한 개인 끝점이 필요 합니다.

> [!NOTE]
> 범용 v2 저장소 계정 에서만 저장소에 대 한 개인 끝점을 만들 수 있습니다. 가격 책정 정보는 [Azure 페이지 blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/page-blobs/)을 참조 하세요.

개인 [저장소 만들기에 대 한 지침](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) 에 따라 개인 끝점을 사용 하 여 저장소 계정을 만듭니다. **개인 DNS 영역과 통합**에서 **예** 를 선택 해야 합니다. 기존 DNS 영역을 선택 하거나 새 DNS 영역을 만듭니다.

### <a name="grant-required-permissions-to-the-vault"></a>자격 증명 모음에 필요한 권한 부여

설정에 따라 대상 Azure 지역에 하나 이상의 저장소 계정이 필요할 수 있습니다. 그런 다음 Site Recovery에 필요한 모든 캐시/로그 저장소 계정에 대 한 관리 id 권한을 부여 합니다. 이 경우 필요한 저장소 계정을 미리 만들어야 합니다.

가상 컴퓨터의 복제를 사용 하도록 설정 하기 전에 저장소 계정 유형에 따라 자격 증명 모음의 관리 id에 다음 역할 권한이 있어야 합니다.

- 리소스 관리자 기반 저장소 계정 (표준 형식):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 리소스 관리자 기반 저장소 계정 (프리미엄 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 소유자](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 클래식 저장소 계정:
  - [클래식 Storage 계정 기여자](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [클래식 스토리지 계정 키 운영자 서비스 역할](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

이러한 단계에서는 저장소 계정에 역할 할당을 추가 하는 방법에 대해 설명 합니다.

1. 스토리지 계정으로 이동합니다. 왼쪽 창에서 **액세스 제어 (IAM)** 를 선택 합니다.

1. **역할 할당 추가** 섹션에서 **추가**를 선택 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="저장소 계정에 대 한 액세스 제어 (IAM) 페이지를 보여 주는 스크린샷":::

1. **역할 할당 추가** 페이지의 **역할** 목록에서이 섹션의 시작 부분에 있는 목록에서 역할을 선택 합니다. 자격 증명 모음의 이름을 입력 하 고 **저장**을 선택 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="역할 할당 추가 페이지를 보여 주는 스크린샷":::

이러한 권한을 추가한 후에는 Microsoft 신뢰할 수 있는 서비스에 대 한 액세스를 허용 해야 합니다. **방화벽 및 가상 네트워크** 로 이동 하 여 **예외**에서 **신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용** 을 선택 합니다.

### <a name="protect-your-virtual-machines"></a>가상 컴퓨터 보호

위의 작업을 완료 한 후 온-프레미스 인프라의 설정을 계속 진행 합니다. 다음 작업 중 하나를 완료 하 여 계속 합니다. 

- [VMware 및 물리적 컴퓨터에 대 한 구성 서버 배포](./vmware-azure-deploy-configuration-server.md)
- [복제용 Hyper-v 환경 설정](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

설치가 완료 되 면 원본 컴퓨터에 대 한 복제를 사용 하도록 설정 합니다. 바이패스 네트워크에서 자격 증명 모음에 대 한 개인 끝점을 만들 때까지 인프라를 설정 하지 마세요.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>개인 DNS 영역 만들기 및 수동으로 DNS 레코드 추가

자격 증명 모음에 대 한 개인 끝점을 만들 때 개인 DNS 영역과 통합 하는 옵션을 선택 하지 않은 경우에는이 섹션의 단계를 수행 합니다.

Site Recovery 공급자 (Hyper-v 컴퓨터의 경우) 또는 프로세스 서버 (VMware/물리적 컴퓨터의 경우)를 허용 하 여 개인 Ip에 대 한 개인 Fqdn을 확인 하려면 하나의 개인 DNS 영역을 만듭니다.

1. 개인 DNS 영역을 만듭니다.

   1. **모든 서비스** 검색 상자에서 "개인 DNS 영역"을 검색 한 다음 결과에서 **영역 사설 DNS** 선택 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure Portal의 새 리소스 페이지에서 개인 dns 영역 검색을 보여 주는 스크린샷":::

   1. **사설 DNS 영역** 페이지에서 **추가** 단추를 선택 하 여 새 영역 만들기를 시작 합니다.

   1. **개인 DNS 영역 만들기** 페이지에서 필요한 세부 정보를 입력 합니다. 개인 DNS 영역 이름에 **privatelink.siterecovery.windowsazure.com** 를 입력 합니다. 모든 리소스 그룹 및 구독을 선택할 수 있습니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="사설 DNS 영역 만들기 페이지의 기본 탭을 보여 주는 스크린샷":::

   1. **검토 \+ 만들기** 탭으로 이동 하 여 DNS 영역을 검토 하 고 만듭니다.

1. 가상 네트워크에 개인 DNS 영역을 연결 합니다.

   이제 만든 개인 DNS 영역을 바이패스에 연결 해야 합니다.

   1. 이전 단계에서 만든 개인 DNS 영역으로 이동한 다음 왼쪽 창의 **가상 네트워크 연결** 로 이동 합니다. **추가**를 선택합니다.

   1. 필요한 세부 정보를 입력 합니다. **구독** 및 **가상 네트워크** 목록에서 바이패스 네트워크에 해당 하는 세부 정보를 선택 합니다. 다른 필드에는 기본값을 그대로 둡니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="가상 네트워크 추가 링크 페이지를 보여 주는 스크린샷":::

1. DNS 레코드를 추가 합니다.

   이제 필요한 개인 DNS 영역 및 개인 끝점을 만들었으므로 dns 영역에 DNS 레코드를 추가 해야 합니다.

   > [!NOTE]
   > 사용자 지정 개인 DNS 영역을 사용 하는 경우 다음 단계에 설명 된 것 처럼 유사한 항목을 확인 해야 합니다.

   이 단계에서는 개인 끝점의 각 FQDN에 대 한 항목을 개인 DNS 영역으로 설정 해야 합니다.

   1. 개인 DNS 영역으로 이동한 다음 왼쪽 창에서 **개요** 섹션으로 이동 합니다. 레코드 추가를 시작 하려면 **레코드 집합** 을 선택 합니다.

   1. **레코드 집합 추가** 페이지에서 각 정규화 **된** 도메인 이름 및 개인 IP에 대 한 항목을 유형 레코드로 추가 합니다. **개요**의 **개인 끝점** 페이지에서 정규화 된 도메인 이름 및 ip의 목록을 가져올 수 있습니다. 다음 스크린샷에서 볼 수 있듯이 개인 끝점의 정규화 된 첫 번째 도메인 이름이 개인 DNS 영역의 레코드 집합에 추가 됩니다.

      이러한 정규화 된 도메인 이름은이 패턴과 일치 합니다.`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="레코드 집합 추가 페이지를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

가상 컴퓨터 복제를 위한 개인 끝점을 사용 하도록 설정 했으므로 다음과 같은 다른 문서에서 추가 및 관련 정보를 참조 하세요.

- [온-프레미스 구성 서버 배포](./vmware-azure-deploy-configuration-server.md)
- [온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정](./hyper-v-azure-tutorial.md)