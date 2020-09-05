---
title: 프라이빗 엔드포인트
description: Azure Backup에 대 한 개인 끝점을 만드는 프로세스와 전용 끝점을 사용 하 여 리소스의 보안을 유지 하는 시나리오를 이해 합니다.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0a875dfedbf7a3b76b479fd4f23b74a7ced47252
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179235"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup에 대 한 개인 끝점

Azure Backup를 사용 하면 [개인 끝점](../private-link/private-endpoint-overview.md)을 사용 하 여 Recovery Services 자격 증명 모음에서 데이터를 안전 하 게 백업 하 고 복원할 수 있습니다. 개인 끝점은 VNet의 개인 IP 주소를 하나 이상 사용 하 여 서비스를 VNet에 효과적으로 제공 합니다.

이 문서는 Azure Backup에 대 한 개인 끝점을 만드는 프로세스와 개인 끝점을 사용 하 여 리소스의 보안을 유지 하는 시나리오를 이해 하는 데 도움이 됩니다.

## <a name="before-you-start"></a>시작하기 전에

- 개인 끝점은 자격 증명 모음에 등록 된 항목이 없는 새 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 대 한 항목을 보호 하려고 하기 전에 개인 끝점을 만들어야 합니다.
- 하나의 가상 네트워크는 여러 Recovery Services 자격 증명 모음에 대 한 개인 끝점을 포함할 수 있습니다. 또한 하나의 Recovery Services 자격 증명 모음에 대해 여러 가상 네트워크에서 개인 끝점을 포함할 수 있습니다. 그러나 자격 증명 모음에 대해 만들 수 있는 최대 개인 끝점 수는 12입니다.
- 자격 증명 모음에 대 한 개인 끝점을 만들면 자격 증명 모음이 잠깁니다. 자격 증명 모음에 대 한 개인 끝점을 포함 하는 네트워크에서 네트워크를 통해 액세스할 수 없습니다 (백업 및 복원의 경우). 자격 증명 모음에 대 한 모든 개인 끝점을 제거 하는 경우 모든 네트워크에서 자격 증명 모음에 액세스할 수 있습니다.
- 백업용 개인 끝점 연결에는 저장소에 대 한 Azure Backup에서 사용 하는 것을 포함 하 여 서브넷에 총 11 개의 개인 Ip가 사용 됩니다. 이 숫자는 특정 Azure 지역에 대해 더 높을 수 있습니다 (최대 25 개). 따라서 백업용 개인 끝점을 만들려고 할 때 사용할 수 있는 개인 Ip가 충분 하다는 것이 좋습니다.
- Recovery Services 자격 증명 모음은 (둘 다) Azure Backup 및 Azure Site Recovery에서 사용 되지만이 문서에서는 Azure Backup 전용 전용 끝점을 사용 하는 방법을 설명 합니다.
- Azure Active Directory는 현재 개인 끝점을 지원 하지 않습니다. 따라서 지역에서 작업을 수행 하는 데 Azure Active Directory 필요한 Ip 및 Fqdn은 Azure Vm에서 데이터베이스의 백업을 수행 하 고 MARS 에이전트를 사용 하 여 백업할 때 보안 네트워크에서의 아웃 바운드 액세스를 허용 해야 합니다. 해당 하는 경우 Azure AD에 대 한 액세스를 허용 하기 위해 NSG 태그 및 Azure 방화벽 태그를 사용할 수도 있습니다.
- 네트워크 정책을 사용 하는 가상 네트워크는 전용 끝점에 대해 지원 되지 않습니다. 계속 하기 전에 네트워크 정책을 사용 하지 않도록 설정 해야 합니다.
- 1 2020 년 5 월 이전에 등록 한 경우 구독에 Recovery Services 리소스 공급자를 다시 등록 해야 합니다. 공급자를 다시 등록 하려면 Azure Portal의 구독으로 이동 하 여 왼쪽 탐색 모음에서 **리소스 공급자** 로 이동 하 고, **Microsoft recoveryservices** 를 선택 하 고, **다시 등록**을 선택 합니다.

## <a name="recommended-and-supported-scenarios"></a>권장 및 지원 되는 시나리오

개인 끝점은 자격 증명 모음에 대해 사용 하도록 설정 되어 있지만 Azure VM 및 MARS 에이전트 백업 에서만 SQL 및 SAP HANA 워크 로드의 백업 및 복원에 사용 됩니다. 다른 워크 로드의 백업에도 자격 증명 모음을 사용할 수 있습니다. 단, 개인 끝점은 필요 하지 않습니다. MARS 에이전트를 사용 하 여 SQL 및 SAP HANA 작업과 백업 백업 외에도 개인 끝점은 Azure VM 백업에 대 한 파일 복구를 수행 하는 데 사용 됩니다. 자세한 내용은 다음 표를 참조하세요.

| Azure VM에서 워크 로드 백업 (SQL, SAP HANA), MARS 에이전트를 사용 하 여 백업 | 가상 네트워크에서 Azure Backup 또는 Azure Storage에 대 한 Ip/Fqdn을 허용 하지 않고 백업 및 복원을 허용 하려면 개인 끝점을 사용 하는 것이 좋습니다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 백업**                                         | VM 백업에는 Ip 또는 Fqdn에 대 한 액세스를 허용 하지 않아도 됩니다. 따라서 디스크의 백업 및 복원을 위한 개인 끝점은 필요 하지 않습니다.  <br><br>   그러나 개인 끝점을 포함 하는 자격 증명 모음에서 파일 복구는 자격 증명 모음에 대 한 개인 끝점을 포함 하는 가상 네트워크로 제한 됩니다. <br><br>    ACL'ed 관리 되지 않는 디스크를 사용 하는 경우 디스크를 포함 하는 저장소 계정에서 ACL'ed 경우 **신뢰할 수 있는 Microsoft 서비스** 에 대 한 액세스를 허용 하는지 확인 합니다. |
| **백업 Azure Files**                                      | Azure Files 백업은 로컬 저장소 계정에 저장 됩니다. 따라서 백업 및 복원에 대 한 개인 끝점은 필요 하지 않습니다. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>백업에 대 한 개인 끝점 만들기 및 사용

이 섹션에서는 가상 네트워크 내에서 Azure Backup에 대 한 개인 끝점을 만들고 사용 하는 단계에 대해 설명 합니다.

>[!IMPORTANT]
> 이 문서에 설명 된 것과 동일한 순서로 단계를 수행 하는 것이 좋습니다. 이렇게 하지 않으면 개인 끝점을 사용 하 고 새 자격 증명 모음을 사용 하 여 프로세스를 다시 시작 해야 하는 자격 증명 모음이 호환 되지 않는 것으로 이어질 수 있습니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Azure Resource Manager 클라이언트를 사용 하 여 자격 증명 모음을 만드는 방법을 알아보려면 [이 섹션](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) 을 참조 하세요. 이렇게 하면 관리 되는 id가 이미 설정 된 자격 증명 모음이 만들어집니다. [여기](./backup-azure-recovery-services-vault-overview.md)에서 Recovery Services 자격 증명 모음에 대해 자세히 알아보세요.

## <a name="enable-managed-identity-for-your-vault"></a>자격 증명 모음에 관리 되는 Id 사용

관리 id를 사용 하면 자격 증명 모음이 개인 끝점을 만들고 사용할 수 있습니다. 이 섹션에서는 자격 증명 모음에 관리 되는 id를 사용 하도록 설정 하는 방법을 설명 합니다.

1. Recovery Services 자격 증명 모음 > **id**로 이동 합니다.

    ![Id 상태를 설정으로 변경](./media/private-endpoints/identity-status-on.png)

1. **상태** 를 **켜기** 로 변경 하 고 **저장**을 선택 합니다.

1. 자격 증명 모음의 관리 되는 id 인 **개체 ID** 가 생성 됩니다.

    >[!NOTE]
    >일단 사용 하도록 설정 하면 관리 되는 Id를 일시적으로 **사용 하지 않도록 설정 해야 합니다** . 관리 id를 사용 하지 않도록 설정 하면 일관 되지 않은 동작이 발생할 수 있습니다.

## <a name="dns-changes"></a>DNS 변경

개인 끝점을 사용 하려면 백업 확장에서 개인 Ip에 대 한 개인 링크 Fqdn을 확인할 수 있도록 사설 DNS 영역이 필요 합니다. 세 개의 개인 DNS 영역이 모두 필요 합니다. 이러한 영역 중 두 개를 mandatorily 만들어야 하는 반면, 세 번째 영역은 개인 끝점을 만드는 동안 개인 끝점과 통합 되도록 옵트인 하거나 별도로 만들 수 있습니다.

사용자 지정 DNS 서버를 사용할 수도 있습니다. 사용자 지정 dns 서버를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 dns 서버의 DNS 변경 내용](#dns-changes-for-custom-dns-servers) 을 참조 하세요.

### <a name="creating-mandatory-dns-zones"></a>필수 DNS 영역 만들기

생성 해야 하는 필수 DNS 영역에는 다음 두 가지가 있습니다.

- `privatelink.blob.core.windows.net` (데이터 백업/복원)
- `privatelink.queue.core.windows.net` (서비스 통신용)

1. **모든 서비스** 검색 표시줄에서 **사설 DNS 영역** 을 검색 하 고 드롭다운 목록에서 **사설 DNS 영역** 을 선택 합니다.

    ![사설 DNS 영역 선택](./media/private-endpoints/private-dns-zone.png)

1. **사설 DNS 영역** 창에서 **+ 추가** 단추를 선택 하 여 새 영역 만들기를 시작 합니다.

1. **개인 DNS 영역 만들기** 창에서 필요한 세부 정보를 입력 합니다. 구독은 개인 끝점이 생성 될 위치와 동일 해야 합니다.

    영역 이름을 다음과 같이 지정 해야 합니다.

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **영역**                           | **서비스** | **구독 및 리소스 그룹 (RG) 세부 정보**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **구독**: 개인 끝점을 만들어야 하는 위치와 동일 합니다.  **rg**의 rg 또는 개인 끝점의 rg |
    | `privatelink.queue.core.windows.net` | 큐       | **RG**: VNET의 RG 또는 개인 끝점의 rg |

    ![사설 DNS 영역 만들기](./media/private-endpoints/create-private-dns-zone.png)

1. 완료 되 면 검토 하 고 DNS 영역을 만듭니다.

### <a name="optional-dns-zone"></a>선택적 DNS 영역

서비스 통신을 위해 개인 끝점을 전용 DNS Azure Backup 영역과 통합 하도록 선택할 수 있습니다 ( [백업에 대 한 개인 끝점 만들기 및 사용](#creating-and-using-private-endpoints-for-backup)섹션에서 설명). 를 개인 DNS 영역과 통합 하지 않으려는 경우 자체 DNS 서버를 사용 하거나 개별적으로 개인 DNS 영역을 만들 수 있습니다. 이는 이전 섹션에서 설명한 두 가지 필수 개인 DNS 영역에 추가 된 것입니다.

Azure에서 별도의 개인 DNS 영역을 만들려는 경우에는 필수 DNS 영역을 만드는 데 사용 된 것과 동일한 단계를 사용 하 여 동일한 작업을 수행할 수 있습니다. 이름 지정 및 구독 세부 정보는 아래와 같습니다.

| **영역**                                                     | **서비스** | **구독 및 리소스 그룹 정보**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **참고**: *지역은 지역 코드* 를 참조 합니다. 예를 들어 미국 서 부 중부 및 유럽 서 부에 대 한 *wcus* 및 *ne* 가 각각 있습니다. | Backup      | **구독**: 개인 끝점을 만들어야 하는 위치와 동일 합니다.  **rg**: 구독 내 모든 RG |

지역 코드는 [이 목록을](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) 참조 하십시오.

국가 지역의 URL 명명 규칙:

- [중국](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [독일](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>가상 네트워크와 개인 DNS 영역 연결

위에서 만든 DNS 영역은 이제 백업할 서버가 있는 가상 네트워크에 연결 되어야 합니다. 사용자가 만든 모든 DNS 영역에 대해이 작업을 수행 해야 합니다.

1. 이전 단계에서 만든 DNS 영역으로 이동 하 여 왼쪽 막대의 **가상 네트워크 링크** 로 이동 합니다. 이 경우 **+ 추가** 단추를 선택 합니다.
1. 필요한 세부 정보를 입력 합니다. **구독** 및 **가상 네트워크** 필드는 서버가 있는 가상 네트워크의 해당 세부 정보로 채워야 합니다. 다른 필드는 그대로 유지 해야 합니다.

    ![가상 네트워크 링크 추가](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>자격 증명 모음에 필요한 개인 끝점을 만들 수 있는 권한 부여

Azure Backup에 대 한 필수 개인 끝점을 만들려면 자격 증명 모음 (자격 증명 모음의 관리 Id)에 다음 리소스 그룹에 대 한 권한이 있어야 합니다.

- 대상 VNet을 포함 하는 리소스 그룹입니다.
- 개인 끝점을 만들 리소스 그룹입니다.
- 사설 DNS 영역을 포함 하는 리소스 그룹

이러한 세 개의 리소스 그룹에 대 한 **참가자** 역할을 자격 증명 모음 (관리 id)에 부여 하는 것이 좋습니다. 다음 단계에서는 특정 리소스 그룹에 대해이 작업을 수행 하는 방법을 설명 합니다 .이 작업은 세 가지 리소스 그룹 각각에 대해 수행 해야 합니다.

1. 리소스 그룹으로 이동 하 여 왼쪽 막대에서 **Access Control (IAM)** 로 이동 합니다.
1. **Access Control**한 후 **역할 할당 추가**로 이동 합니다.

    ![역할 할당 추가](./media/private-endpoints/add-role-assignment.png)

1. **역할 할당 추가** 창에서 **역할**로 **참가자** 를 선택 하 고 자격 증명 모음 **이름을** **주**서버로 사용 합니다. 자격 증명 모음을 선택 하 고 완료 되 면 **저장** 을 선택 합니다.

    ![역할 및 주체 선택](./media/private-endpoints/choose-role-and-principal.png)

더 세부적인 수준에서 사용 권한을 관리 하려면 [수동으로 역할 및 권한 만들기](#create-roles-and-permissions-manually)를 참조 하세요.

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Azure Backup에 대 한 개인 끝점 만들기 및 승인

### <a name="creating-private-endpoints-for-backup"></a>백업용 개인 끝점 만들기

이 섹션에서는 자격 증명 모음에 대 한 개인 끝점을 만드는 프로세스에 대해 설명 합니다.

1. 검색 창에서 **개인 링크**를 검색 하 고 선택 합니다. 그러면 **개인 링크 센터로**이동 합니다.

    ![개인 링크 검색](./media/private-endpoints/search-for-private-link.png)

1. 왼쪽 탐색 모음에서 **전용 끝점**을 선택 합니다. **전용 끝점** 창에서 **+ 추가** 를 선택 하 여 자격 증명 모음에 대 한 개인 끝점 만들기를 시작 합니다.

    ![개인 링크 센터에서 개인 끝점 추가](./media/private-endpoints/add-private-endpoint.png)

1. **개인 끝점 만들기** 프로세스에서 개인 끝점 연결을 만드는 방법에 대 한 세부 정보를 지정 해야 합니다.

    1. **기본 사항**: 개인 끝점에 대 한 기본 세부 정보를 입력 합니다. 지역은 자격 증명 모음 및 리소스와 동일 해야 합니다.

        ![기본 정보 입력](./media/private-endpoints/basic-details.png)

    1. **리소스**:이 탭을 사용 하려면 연결을 만들려는 PaaS 리소스를 언급 해야 합니다. 원하는 구독의 리소스 유형에 서 **Microsoft RecoveryServices/자격 증명 모음** 을 선택 합니다. 작업이 완료 되 면 **리소스** 및 **azurebackup** 의 Recovery Services 자격 증명 모음 이름을 **대상 하위 리소스로**선택 합니다.

        ![리소스 탭에 입력](./media/private-endpoints/resource-tab.png)

    1. **구성**: 구성에서 개인 끝점을 만들 가상 네트워크 및 서브넷을 지정 합니다. 이는 VM이 있는 Vnet입니다. 개인 끝점을 개인 DNS 영역과 **통합** 하도록 선택할 수 있습니다. 또는 사용자 지정 DNS 서버를 사용 하거나 개인 DNS 영역을 만들 수도 있습니다.

        ![구성 탭 채우기](./media/private-endpoints/configuration-tab.png)

    1. 필요에 따라 개인 끝점에 대 한 **태그** 를 추가할 수 있습니다.

    1. 자세히 입력을 완료 한 후 계속 **검토 + 만들기** 를 계속 합니다. 유효성 검사가 완료 되 면 **만들기** 를 선택 하 여 개인 끝점을 만듭니다.

## <a name="approving-private-endpoints"></a>개인 끝점 승인

개인 끝점을 만드는 사용자가 Recovery Services 자격 증명 모음의 소유자 이기도 한 경우 위에서 만든 개인 끝점은 자동으로 승인 됩니다. 그렇지 않으면 자격 증명 모음의 소유자가 개인 끝점을 사용 하려면 먼저 승인 해야 합니다. 이 섹션에서는 Azure Portal를 통한 개인 끝점의 수동 승인에 대해 설명 합니다.

Azure Resource Manager 클라이언트를 사용 하 여 개인 끝점을 승인 하려면 [Azure Resource Manager 클라이언트를 사용 하 여 개인 끝점의 수동 승인](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) 을 참조 하세요.

1. Recovery Services 자격 증명 모음에서 왼쪽 표시줄의 **개인 끝점 연결** 로 이동 합니다.
1. 승인 하려는 개인 끝점 연결을 선택 합니다.
1. 위쪽 막대에서 **승인** 을 선택 합니다. 끝점 연결을 거부 하거나 삭제 하려는 경우 **거부** 또는 **제거** 를 선택할 수도 있습니다.

    ![개인 끝점 승인](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>DNS 레코드 추가

>[!NOTE]
> 통합 DNS 영역을 사용 하는 경우에는이 단계가 필요 하지 않습니다. 그러나 사용자 고유의 Azure 사설 DNS 영역을 만들었거나 사용자 지정 개인 DNS 영역을 사용 하는 경우이 섹션에 설명 된 대로 항목이 생성 되었는지 확인 합니다.

선택적 개인 DNS 영역 및 자격 증명 모음에 대 한 개인 끝점을 만들었으면 dns 레코드를 DNS 영역에 추가 해야 합니다. 수동으로 또는 PowerShell 스크립트를 사용 하 여이 작업을 수행할 수 있습니다. 이 작업은 백업 DNS 영역에 대해서만 수행 해야 하며 Blob 및 큐에 대 한 작업은 자동으로 업데이트 됩니다.

### <a name="add-records-manually"></a>수동으로 레코드 추가

이렇게 하려면 개인 끝점의 각 FQDN에 대 한 항목을 사설 DNS 영역으로 설정 해야 합니다.

1. **개인 DNS 영역** 으로 이동 하 여 왼쪽 막대의 **개요** 옵션으로 이동 합니다. 그런 후에는 **+ 레코드 집합** 을 선택 하 여 레코드 추가를 시작 합니다.

    ![레코드를 추가 하려면 + 레코드 집합을 선택 합니다.](./media/private-endpoints/select-record-set.png)

1. 열리는 **레코드 집합 추가** 창에서 각 FQDN 및 개인 IP에 대 한 항목 하나를 **유형** 레코드로 추가 합니다. Fqdn 및 Ip 목록은 개인 끝점 ( **개요**)에서 가져올 수 있습니다. 아래 예제에서 볼 수 있듯이 개인 끝점의 첫 번째 FQDN은 개인 DNS 영역의 레코드 집합에 추가 됩니다.

    ![Fqdn 및 Ip 목록](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![레코드 집합 추가](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>PowerShell 스크립트를 사용 하 여 레코드 추가

1. Azure Portal에서 **Cloud Shell** 를 시작 하 고 PowerShell 창에서 **파일 업로드** 를 선택 합니다.

    ![PowerShell 창에서 파일 업로드를 선택 합니다.](./media/private-endpoints/upload-file-in-powershell.png)

1. 이 스크립트를 업로드 합니다. [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. 홈 폴더로 이동 합니다 (예:). `cd /home/user`

1. 다음 스크립트를 실행합니다.

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    매개 변수는 다음과 같습니다.

    - **구독**: 리소스 (자격 증명 모음 개인 끝점 및 개인 DNS 영역)가 상주 하는 구독
    - **vaultPEName**: 자격 증명 모음에 대해 만들어진 개인 끝점의 이름입니다.
    - **vaultPEResourceGroup**: 자격 증명 모음의 개인 끝점을 포함 하는 리소스 그룹
    - **Dnsresourcegroup**: 개인 DNS 영역을 포함 하는 리소스 그룹
    - **Privatezone**: 개인 DNS 영역의 이름

## <a name="using-private-endpoints-for-backup"></a>백업에 전용 끝점 사용

VNet에서 자격 증명 모음에 대해 만들어진 개인 끝점이 승인 되 면이를 사용 하 여 백업 및 복원을 수행할 수 있습니다.

>[!IMPORTANT]
>계속 하기 전에 문서에서 위에서 언급 한 모든 단계를 완료 했는지 확인 합니다. 이를 요약 하려면 다음 검사 목록의 단계를 완료 해야 합니다.
>
>1. (새) Recovery Services 자격 증명 모음을 만듦
>1. 시스템 할당 관리 Id를 사용 하도록 자격 증명 모음 설정
>1. 3 개의 사설 DNS 영역을 만듦 (백업에 통합 DNS 영역을 사용 하는 경우 2 개)
>1. Azure Virtual Network에 사설 DNS 영역 연결
>1. 자격 증명 모음의 관리 Id에 대 한 관련 권한 할당
>1. 자격 증명 모음에 대 한 개인 끝점을 만들었습니다.
>1. 자동 승인 되지 않은 경우 개인 끝점 승인 됨
>1. 백업에 대 한 개인 DNS 영역에 필요한 DNS 레코드를 추가 했습니다 (통합 된 사설 DNS 영역을 사용 하지 않는 경우에만 해당).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Azure VM에서 워크 로드의 백업 및 복원 (SQL, SAP HANA)

개인 끝점을 만들고 승인한 후에는 클라이언트 쪽에서 개인 끝점을 사용 하기 위해 추가 변경이 필요 하지 않습니다. 보안 네트워크에서 자격 증명 모음으로의 모든 통신 및 데이터 전송은 개인 끝점을 통해 수행 됩니다.
그러나 서버 (SQL/SAP HANA)를 등록 한 후에 자격 증명 모음에 대 한 개인 끝점을 제거 하는 경우 컨테이너를 자격 증명 모음에 다시 등록 해야 합니다. 보호를 중지할 필요가 없습니다.

### <a name="backup-and-restore-through-mars-agent"></a>MARS 에이전트를 통해 백업 및 복원

MARS 에이전트를 사용 하 여 온-프레미스 리소스를 백업 하는 경우 온-프레미스 네트워크 (백업할 리소스 포함)가 자격 증명 모음에 대 한 개인 끝점을 포함 하는 Azure VNet과 피어 링 하 여 사용할 수 있도록 해야 합니다. 그러면 MARS 에이전트를 계속 설치 하 고 여기에 설명 된 대로 백업을 구성할 수 있습니다. 그러나 백업에 대 한 모든 통신은 피어 링 네트워크를 통해 수행 되어야 합니다.

그러나 MARS 에이전트가 등록 된 후에 자격 증명 모음에 대 한 개인 끝점을 제거 하는 경우 컨테이너를 자격 증명 모음에 다시 등록 해야 합니다. 보호를 중지할 필요가 없습니다.

## <a name="additional-topics"></a>추가 항목

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 만들기

Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음을 만들고 관리 되는 id를 사용 하도록 설정할 수 있습니다 (나중에 표시 되는 대로 관리 되는 id를 사용 하도록 설정 해야 함). 이 작업을 수행 하는 샘플은 아래와 같습니다.

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

위의 JSON 파일에는 다음 내용이 있어야 합니다.

요청 JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

응답 JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>이 예에서는 Azure Resource Manager 클라이언트를 통해 만든 자격 증명 모음이 이미 시스템 할당 관리 id로 생성 되었습니다.

### <a name="managing-permissions-on-resource-groups"></a>리소스 그룹에 대 한 사용 권한 관리

자격 증명 모음에 대 한 관리 되는 Id에는 개인 끝점이 생성 될 가상 네트워크 및 리소스 그룹에 대 한 다음 권한이 있어야 합니다.

- `Microsoft.Network/privateEndpoints/*` 리소스 그룹의 전용 끝점에 대 한 CRUD를 수행 하는 데 필요 합니다. 리소스 그룹에 할당 되어야 합니다.
- `Microsoft.Network/virtualNetworks/subnets/join/action` 개인 IP가 개인 끝점과 연결 되는 가상 네트워크에 필요 합니다.
- `Microsoft.Network/networkInterfaces/read` 이는 전용 끝점에 대해 생성 된 네트워크 인터페이스를 가져오기 위해 리소스 그룹에 필요 합니다.
- 영역 참가자 역할 사설 DNS이 역할은 이미 존재 하며 `Microsoft.Network/privateDnsZones/A/*` 및 사용 권한을 제공 하는 데 사용할 수 있습니다 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

다음 방법 중 하나를 사용 하 여 필요한 권한이 있는 역할을 만들 수 있습니다.

#### <a name="create-roles-and-permissions-manually"></a>수동으로 역할 및 사용 권한 만들기

다음 JSON 파일을 만들고 섹션의 끝에 있는 PowerShell 명령을 사용 하 여 역할을 만듭니다.

PrivateEndpointContributorRoleDef.js

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.js

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.js

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>스크립트 사용

1. Azure Portal에서 **Cloud Shell** 를 시작 하 고 PowerShell 창에서 **파일 업로드** 를 선택 합니다.

    ![PowerShell 창에서 파일 업로드를 선택 합니다.](./media/private-endpoints/upload-file-in-powershell.png)

1. 다음 스크립트를 업로드 합니다. [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. 홈 폴더로 이동 합니다 (예:). `cd /home/user`

1. 다음 스크립트를 실행합니다.

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    매개 변수는 다음과 같습니다.

    - **구독**: * * SubscriptionId 자격 증명 모음에 대 한 개인 끝점을 만들 리소스 그룹 및 자격 증명 모음의 개인 끝점이 연결 되는 서브넷

    - **vaultPEResourceGroup**: 자격 증명 모음에 대 한 개인 끝점을 만들 리소스 그룹입니다.

    - **vaultPESubnetResourceGroup**: 개인 끝점이 가입 될 서브넷의 리소스 그룹입니다.

    - **vaultMsiName**: **VaultName** 와 동일한 자격 증명 모음 MSI의 이름

1. 인증을 완료 하면 스크립트가 위에 제공 된 지정 된 구독의 컨텍스트를 사용 합니다. 테 넌 트에서 누락 된 경우 적절 한 역할을 만들고 자격 증명 모음 MSI에 역할을 할당 합니다.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 개인 끝점 만들기

#### <a name="auto-approved-private-endpoints"></a>자동 승인 된 개인 끝점

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 개인 끝점의 수동 승인

1. **Getvault** 를 사용 하 여 개인 끝점에 대 한 개인 끝점 연결 ID를 가져옵니다.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    그러면 개인 끝점 연결 ID가 반환 됩니다. 연결 ID의 첫 번째 부분을 사용 하 여 연결 이름을 다음과 같이 검색할 수 있습니다.

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 아래 샘플과 같이 **개인 끝점 연결 ID** (및 필요한 경우 **개인 끝점 이름**)를 응답에서 가져온 후 다음 JSON 및 Azure Resource Manager URI로 바꾸고 상태를 "승인 됨/거부 됨/연결 끊김"으로 변경 해 보세요.

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>사용자 지정 DNS 서버에 대 한 DNS 변경

#### <a name="create-dns-zones-for-custom-dns-servers"></a>사용자 지정 DNS 서버에 대 한 DNS 영역 만들기

세 개의 개인 DNS 영역을 만들고 가상 네트워크에 연결 해야 합니다.

| **영역**                                                     | **서비스** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | 큐       |

>[!NOTE]
>위의 텍스트에서 *geo* 는 지역 코드를 나타냅니다. 예를 들어 미국 서 부 중부 및 유럽 서 부에 대 한 *wcus* 및 *ne* 가 각각 있습니다.

지역 코드는 [이 목록을](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) 참조 하십시오.

#### <a name="adding-dns-records-for-custom-dns-servers"></a>사용자 지정 DNS 서버에 대 한 DNS 레코드 추가

이렇게 하려면 개인 끝점의 각 FQDN에 대 한 항목을 사설 DNS 영역으로 설정 해야 합니다.

백업, Blob 및 큐 서비스에 대해 만들어진 개인 끝점을 사용 하 게 될 것입니다.

- 자격 증명 모음의 개인 끝점은 개인 끝점을 만드는 동안 지정 된 이름을 사용 합니다.
- Blob 및 큐 서비스에 대 한 개인 끝점에는 자격 증명 모음에 대해 동일한 이름의 접두사가 붙습니다.

예를 들어 다음 그림은 이름이 *pee2epe*개인 끝점 연결에 대해 생성 된 세 개의 개인 끝점을 보여 줍니다.

![개인 끝점 연결에 대 한 세 개의 개인 끝점](./media/private-endpoints/three-private-endpoints.png)

백업 서비스에 대 한 DNS 영역 ( `privatelink.<geo>.backup.windowsazure.com` ):

1. **개인 링크 센터**에서 백업용 개인 끝점으로 이동 합니다. 개요 페이지에는 개인 끝점의 FQDN 및 개인 Ip가 나열 됩니다.

1. 각 FQDN 및 개인 IP에 대 한 항목 하나를 유형 레코드로 추가 합니다.

    ![각 FQDN 및 개인 IP에 대 한 항목 추가](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Blob service ()에 대 한 DNS 영역 `privatelink.blob.core.windows.net` :

1. **개인 링크 센터**에서 Blob에 대 한 개인 끝점으로 이동 합니다. 개요 페이지에는 개인 끝점의 FQDN 및 개인 Ip가 나열 됩니다.

1. FQDN 및 개인 IP에 대 한 항목을 유형 레코드로 추가 합니다.

    ![FQDN 및 개인 IP에 대 한 항목을 Blob service에 대 한 형식 레코드로 추가 합니다.](./media/private-endpoints/add-type-a-record-for-blob.png)

큐 서비스 ()에 대 한 DNS 영역 `privatelink.queue.core.windows.net` :

1. **개인 링크 센터**에서 큐에 대 한 개인 끝점으로 이동 합니다. 개요 페이지에는 개인 끝점의 FQDN 및 개인 Ip가 나열 됩니다.

1. FQDN 및 개인 IP에 대 한 항목을 유형 레코드로 추가 합니다.

    ![FQDN 및 개인 IP에 대 한 항목을 큐 서비스에 대 한 형식 레코드로 추가 합니다.](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>질문과 대답

17. 기존 백업 자격 증명 모음에 대 한 개인 끝점을 만들 수 있나요?<br>
A. 아니요. 개인 끝점은 새 백업 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 보호 된 항목이 없어야 합니다. 실제로 개인 끝점을 만들기 전에 자격 증명 모음에 대 한 항목을 보호 하려는 시도는 없습니다.

17. 자격 증명 모음에 대 한 항목을 보호 하려고 했지만 실패 했으며 자격 증명 모음에 보호 된 항목이 아직 포함 되어 있지 않습니다. 이 자격 증명 모음에 대 한 개인 끝점을 만들 수 있나요?<br>
A. 아니요, 자격 증명 모음에서 과거에 항목을 보호 하려고 시도 하지 않아야 합니다.

17. 백업 및 복원을 위해 개인 끝점을 사용 하는 자격 증명 모음이 있습니다. 백업 항목이 보호 된 경우에도 나중에이 자격 증명 모음에 대 한 개인 끝점을 추가 하거나 제거할 수 있나요?<br>
A. 예. 자격 증명 모음 및 보호 된 백업 항목에 대 한 개인 끝점을 이미 만든 경우 나중에 필요에 따라 개인 끝점을 추가 하거나 제거할 수 있습니다.

17. Azure Backup에 대 한 개인 끝점을 Azure Site Recovery에도 사용할 수 있나요?<br>
A. 아니요, 백업용 개인 끝점은 Azure Backup에만 사용할 수 있습니다. 서비스에서 지 원하는 경우 Azure Site Recovery에 대 한 새 개인 끝점을 만들어야 합니다.

17. 이 문서의 단계 중 하나가 누락 되 고 내 데이터 원본을 보호 하는 데 문제가 발생 했습니다. 여전히 개인 끝점을 사용할 수 있나요?<br>
A. 이 문서의 단계를 따르지 않고 항목 보호를 계속 하면 자격 증명 모음이 개인 끝점을 사용할 수 없게 될 수 있습니다. 따라서 항목 보호를 계속 하기 전에이 검사 목록을 참조 하는 것이 좋습니다.

17. Azure 개인 DNS 영역 또는 통합 된 사설 DNS 영역을 사용 하는 대신 자체 DNS 서버를 사용할 수 있나요?<br>
A. 예, 사용자의 DNS 서버를 사용할 수 있습니다. 그러나이 섹션에서는 필요한 모든 DNS 레코드가 제안 된 대로 추가 되었는지 확인 합니다.

17. 이 문서의 프로세스를 수행한 후 서버에서 추가 단계를 수행 해야 하나요?<br>
A. 이 문서에 자세히 설명 된 프로세스를 수행한 후에는 백업 및 복원에 개인 끝점을 사용 하기 위해 추가 작업을 수행할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup의 모든 보안 기능](security-overview.md) 에 대해 읽기
