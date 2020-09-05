---
title: Azure 기본 제공 역할 - Azure RBAC
description: 이 문서에서는 Azure RBAC(역할 기반 액세스 제어)의 Azure 기본 제공 역할에 대해 설명하고 Actions, NotActions, DataActions 및 NotDataActions를 나열합니다.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 08/31/2020
ms.custom: generated
ms.openlocfilehash: b58316cf5a56eae46c81056a78446dc6c3d10764
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226763"
---
# <a name="azure-built-in-roles"></a>Azure 기본 제공 역할

[Azure RBAC(역할 기반 액세스 제어)](overview.md)는 사용자, 그룹, 서비스 주체 및 관리 ID에 할당할 수 있는 여러 가지 Azure 기본 제공 역할을 제공합니다. 역할 할당은 Azure 리소스에 대한 액세스를 제어하는 방법입니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [Azure 사용자 지정 역할](custom-roles.md)을 만들면 됩니다.

이 문서에는 Azure 기본 제공 역할 목록이 포함되어 있으며, 이 목록은 지속적으로 업데이트됩니다. 최신 역할을 가져오려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 또는 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다. Azure AD(Azure Active Directory)의 관리자 역할을 찾고 있는 경우 [Azure Active Directory의 관리자 역할 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

다음 표에서는 각 기본 제공 역할에 대한 간략한 설명과 고유 ID를 제공합니다. 각 역할의 `Actions`, `NotActions`, `DataActions` 및 `NotDataActions` 목록을 보려면 역할 이름을 클릭합니다. 이러한 작업의 의미와 작업이 관리 및 데이터 평면에 적용되는 방식에 대한 자세한 내용은 [Azure 리소스에 대한 역할 정의 이해](role-definitions.md)를 참조하세요.

## <a name="all"></a>모두

> [!div class="mx-tableFixed"]
> | 기본 제공 역할 | Description | ID |
> | --- | --- | --- |
> | **일반** |  |  |
> | [기여자](#contributor) | 모든 리소스를 관리할 수 있는 모든 권한을 부여 하지만, Azure RBAC에서 역할을 할당할 수는 없습니다. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [소유자](#owner) | Azure RBAC에서 역할을 할당 하는 기능을 포함 하 여 모든 리소스를 관리할 수 있는 모든 권한을 부여 합니다. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [판독기](#reader) | 모든 리소스를 볼 수 있지만 변경할 수는 없습니다. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [사용자 액세스 관리자](#user-access-administrator) | Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **컴퓨팅** |  |  |
> | [Classic Virtual Machine 참가자](#classic-virtual-machine-contributor) | 클래식 가상 머신을 관리할 수 있지만 가상 머신이나 연결된 가상 네트워크 또는 스토리지 계정에 액세스할 수는 없습니다. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [가상 머신 관리자 로그인](#virtual-machine-administrator-login) | 포털에서 Virtual Machines를 보고 관리자 권한으로 로그인합니다. | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtual Machine 참가자](#virtual-machine-contributor) | 가상 머신을 관리할 수 있지만 가상머신이나 연결된 가상 네트워크 또는 스토리지 계정에 액세스할 수는 없습니다. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [가상 머신 사용자 로그인](#virtual-machine-user-login) | 포털에서 Virtual Machines를 보고 일반 사용자 권한으로 로그인합니다. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **네트워킹** |  |  |
> | [CDN 엔드포인트 기여자](#cdn-endpoint-contributor) | CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN 엔드포인트 독자](#cdn-endpoint-reader) | CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN 프로필 기여자](#cdn-profile-contributor) | CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN 프로필 독자](#cdn-profile-reader) | CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [클래식 네트워크 기여자](#classic-network-contributor) | 기본 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS 영역 참가자](#dns-zone-contributor) | Azure DNS의 DNS 영역과 레코드 집합을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [네트워크 기여자](#network-contributor) | 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [사설 DNS 영역 기여자](#private-dns-zone-contributor) | 개인 DNS 영역 리소스를 관리할 수 있지만 연결 된 가상 네트워크는 관리할 수 없습니다. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager 기여자](#traffic-manager-contributor) | Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **스토리지** |  |  |
> | [Avere 기여자](#avere-contributor) | Avere vFXT 클러스터를 만들고 관리할 수 있습니다. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere 운영자](#avere-operator) | Avere vFXT 클러스터에서 클러스터를 관리하는 데 사용됩니다. | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Backup 기여자](#backup-contributor) | 백업 서비스를 관리할 수 있지만, 자격 증명 모음을 만들고 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Backup 운영자](#backup-operator) | 백업 제거를 제외한 백업 서비스를 관리하고 자격 증명 모음 만들고 다른 사람에게 액세스 권한을 부여할 수 있습니다. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup 읽기 권한자](#backup-reader) | 백업 서비스를 볼 수 있지만 변경할 수는 없습니다. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [클래식 Storage 계정 기여자](#classic-storage-account-contributor) | 클래식 Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [클래식 스토리지 계정 키 운영자 서비스 역할](#classic-storage-account-key-operator-service-role) | 클래식 스토리지 계정 키 운영자가 클래식 스토리지 계정에서 키를 나열하고 다시 생성할 수 있습니다. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box 기여자](#data-box-contributor) | 다른 사람에게 액세스 권한을 부여하는 것을 제외한 모든 항목을 Data Box 서비스에서 관리할 수 있습니다. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box 읽기 권한자](#data-box-reader) | 주문하기나 주문 세부 정보 편집 및 다른 사용자에게 액세스 권한 부여 외에 Data Box 서비스를 관리할 수 있습니다. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics 개발자](#data-lake-analytics-developer) | 사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [읽기 권한자 및 데이터 액세스](#reader-and-data-access) | 모든 것을 볼 수 있지만, 스토리지 계정 또는 포함된 리소스를 삭제하거나 만들 수는 없습니다. 또한 스토리지 계정 키에 액세스하여 스토리지 계정에 포함된 모든 데이터를 읽고 쓸 수 있습니다. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Storage 계정 기여자](#storage-account-contributor) | 스토리지 계정을 관리할 수 있도록 허용합니다. 공유 키 권한 부여를 통해 데이터에 액세스하는 데 사용할 수 있는 계정 키에 대한 액세스 권한을 제공합니다. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [스토리지 계정 키 운영자 서비스 역할](#storage-account-key-operator-service-role) | 스토리지 계정 액세스 키를 나열하고 다시 생성할 수 있도록 허용합니다. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage Blob 데이터 기여자](#storage-blob-data-contributor) | Azure Storage 컨테이너 및 BLOB을 읽고, 쓰고, 삭제합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage Blob 데이터 소유자](#storage-blob-data-owner) | POSIX 액세스 제어 할당을 포함하여 Azure Storage BLOB 컨테이너 및 데이터에 대한 모든 액세스 권한을 제공합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage Blob 데이터 읽기 권한자](#storage-blob-data-reader) | Azure Storage 컨테이너 및 BLOB을 읽고 나열합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob 위임자](#storage-blob-delegator) | Azure AD 자격 증명으로 서명된 컨테이너 또는 BLOB의 공유 액세스 서명을 만드는 데 사용할 수 있는 사용자 위임 키를 가져옵니다. 자세한 내용은 [사용자 위임 SAS 만들기](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)를 참조하세요. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage 파일 데이터 SMB 공유 기여자](#storage-file-data-smb-share-contributor) | Azure 파일 공유의 파일/디렉터리에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. Windows 파일 서버에는 이 역할에 상응하는 기본 제공 역할이 없습니다. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage 파일 데이터 SMB 공유 높은 권한 기여자](#storage-file-data-smb-share-elevated-contributor) | Azure 파일 공유의 파일/디렉터리에 대한 ACL을 읽고, 쓰고, 삭제하고, 수정할 수 있습니다. 이 역할은 Windows 파일 서버의 변경 내용에 대한 파일 공유 ACL에 해당합니다. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage 파일 데이터 SMB 공유 읽기 권한자](#storage-file-data-smb-share-reader) | Azure 파일 공유의 파일/디렉터리에 대한 읽기 액세스를 허용합니다. 이 역할은 Windows 파일 서버에 대한 파일 공유 ACL 읽기에 해당합니다. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Storage 큐 데이터 기여자](#storage-queue-data-contributor) | Azure Storage 큐 및 큐 메시지를 읽고, 쓰고, 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Storage 큐 데이터 메시지 처리자](#storage-queue-data-message-processor) | Azure Storage 큐의 메시지를 선택, 검색 및 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Storage 큐 데이터 메시지 보내는 사람](#storage-queue-data-message-sender) | Azure Storage 큐에 메시지를 추가할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage 큐 데이터 읽기 권한자](#storage-queue-data-reader) | Azure Storage 큐 및 큐 메시지를 읽고 나열할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps 데이터 읽기 권한자](#azure-maps-data-reader) | Azure 맵 계정에서 맵 관련 데이터를 읽을 수 있는 액세스 권한을 부여합니다. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Search 서비스 기여자](#search-service-contributor) | Search 서비스를 관리할 수 있지만 액세스할 수는 없습니다. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [웹 계획 참가자](#web-plan-contributor) | 웹 사이트의 웹 계획을 관리할 수 있지만 액세스할 수는 없습니다. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [웹 사이트 기여자](#website-contributor) | 웹 사이트(웹 계획은 제외)를 관리할 수 있지만 액세스할 수는 없습니다. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **컨테이너** |  |  |
> | [AcrDelete](#acrdelete) | acr 삭제 | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr 이미지 서명자 | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr 격리 데이터 읽기 권한자 | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr 격리 데이터 작성자 | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service 클러스터 관리자 역할](#azure-kubernetes-service-cluster-admin-role) | 클러스터 관리자 자격 증명 작업을 나열합니다. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service 클러스터 사용자 역할](#azure-kubernetes-service-cluster-user-role) | 클러스터 사용자 자격 증명 작업을 나열합니다. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service 기여자 역할](#azure-kubernetes-service-contributor-role) | Azure Kubernetes 서비스 클러스터를 읽고 쓸 수 있는 액세스 권한을 부여 합니다. | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes 서비스 RBAC 관리자](#azure-kubernetes-service-rbac-admin) | 리소스 할당량 및 네임 스페이스 업데이트 또는 삭제를 제외 하 고 클러스터/네임 스페이스 아래의 모든 리소스를 관리할 수 있습니다. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes 서비스 RBAC 클러스터 관리자](#azure-kubernetes-service-rbac-cluster-admin) | 클러스터의 모든 리소스를 관리할 수 있습니다. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes 서비스 RBAC 판독기](#azure-kubernetes-service-rbac-reader) | 암호를 제외한 클러스터/네임 스페이스의 모든 리소스를 볼 수 있습니다. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes 서비스 RBAC 기록기](#azure-kubernetes-service-rbac-writer) | 리소스 할당량, 네임 스페이스, pod 보안 정책, 인증서 서명 요청, (클러스터) 역할 및 (클러스터) 역할 바인딩을 제외 하 고 클러스터/네임 스페이스의 모든 항목을 업데이트할 수 있습니다. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **데이터베이스** |  |  |
> | [Cosmos DB 계정 독자 역할](#cosmos-db-account-reader-role) | Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB 운영자](#cosmos-db-operator) | Azure Cosmos DB 계정을 관리할 수 있지만 계정의 데이터에 액세스할 수는 없습니다. 계정 키 및 연결 문자열에 대한 액세스를 차단합니다. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Cosmos DB 데이터베이스 또는 계정의 컨테이너에 대한 복원 요청을 제출할 수 있습니다. | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB 계정 기여자](#documentdb-account-contributor) | Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache 참가자](#redis-cache-contributor) | Redis Cache를 관리할 수 있지만 액세스할 수는 없습니다. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB 기여자](#sql-db-contributor) | SQL 데이터베이스를 관리할 수 있지만 액세스할 수는 없습니다. 또한 보안 관련 정책이나 부모 SQL 서버를 관리할 수 없습니다. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance 기여자](#sql-managed-instance-contributor) | SQL Managed Instances 및 필수 네트워크 구성을 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL 보안 관리자](#sql-security-manager) | SQL Server 및 데이터베이스의 보안과 관련된 정책을 관리할 수 있지만 여기에 액세스할 수는 없습니다. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server 기여자](#sql-server-contributor) | SQL Server 및 데이터베이스를 관리할 수 있지만 액세스할 수는 없으며, 해당하는 보안 관련 정책에도 액세스할 수 없습니다. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **분석** |  |  |
> | [Azure Event Hubs 데이터 소유자](#azure-event-hubs-data-owner) | Azure Event Hubs 리소스에 대한 전체 액세스를 허용합니다. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs 데이터 받는 사람](#azure-event-hubs-data-receiver) | Azure Event Hubs 리소스에 대한 받기 액세스 권한을 허용합니다. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs 데이터 보내는 사람](#azure-event-hubs-data-sender) | Azure Event Hubs 리소스에 대한 보내기 액세스 권한을 허용합니다. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory 참가자](#data-factory-contributor) | 데이터 팩터리를 만들고 관리하며 해당 하위 리소스도 만들고 관리합니다. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [데이터 제거자](#data-purger) | 분석 데이터를 제거할 수 있습니다. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight 클러스터 운영자](#hdinsight-cluster-operator) | HDInsight 클러스터 구성을 읽고 수정할 수 있습니다. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight 도메인 서비스 기여자](#hdinsight-domain-services-contributor) | HDInsight Enterprise Security Package에 필요한 도메인 서비스 관련 작업을 읽고, 만들고, 수정하고, 삭제할 수 있음 | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics 기여자](#log-analytics-contributor) | Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics 독자](#log-analytics-reader) | Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **블록체인** |  |  |
> | [블록체인 멤버 노드 액세스(미리 보기)](#blockchain-member-node-access-preview) | 블록체인 멤버 노드에 액세스할 수 있습니다. | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + 기계 학습** |  |  |
> | [Cognitive Services 기여자](#cognitive-services-contributor) | Cognitive Services의 키를 만들고, 읽고, 업데이트하고, 삭제 및 관리할 수 있습니다. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services 데이터 읽기 권한자(미리 보기)](#cognitive-services-data-reader-preview) | Cognitive Services 데이터를 읽을 수 있습니다. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services 사용자](#cognitive-services-user) | Cognitive Services의 키를 읽고 나열할 수 있습니다. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **혼합 현실** |  |  |
> | [원격 렌더링 관리자](#remote-rendering-administrator) | Azure 원격 렌더링을 위한 변환, 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다. | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [원격 렌더링 클라이언트](#remote-rendering-client) | Azure 원격 렌더링을 위한 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors 계정 기여자](#spatial-anchors-account-contributor) | 계정의 공간 앵커를 관리할 수 있지만 삭제할 수는 없습니다. | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors 계정 소유자](#spatial-anchors-account-owner) | 계정의 공간 앵커를 관리할 수 있고 삭제할 수도 있습니다. | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors 계정 읽기 권한자](#spatial-anchors-account-reader) | 계정의 공간 앵커 속성을 찾아서 읽을 수 있습니다. | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **통합** |  |  |
> | [API Management 서비스 참가자](#api-management-service-contributor) | 서비스 및 API를 관리할 수 있습니다. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management 서비스 운영자 역할](#api-management-service-operator-role) | 서비스를 관리할 수 있지만 API는 관리할 수 없습니다. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Azure API Management 읽기 권한자 역할](#api-management-service-reader-role) | 서비스 및 API에 대한 읽기 전용 액세스 | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration 데이터 소유자](#app-configuration-data-owner) | App Configuration 데이터에 대한 모든 액세스 권한을 허용합니다. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration 데이터 읽기 권한자](#app-configuration-data-reader) | App Configuration 데이터에 대한 읽기 액세스 권한을 허용합니다. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus 데이터 소유자](#azure-service-bus-data-owner) | Azure Service Bus 리소스에 대한 전체 액세스를 허용합니다. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus 데이터 받는 사람](#azure-service-bus-data-receiver) | Azure Service Bus 리소스에 대한 받기 액세스 권한을 허용합니다. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus 데이터 보내는 사람](#azure-service-bus-data-sender) | Azure Service Bus 리소스에 대한 보내기 액세스 권한을 허용합니다. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack 등록 소유자](#azure-stack-registration-owner) | Azure Stack 등록을 관리할 수 있습니다. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription 기여자](#eventgrid-eventsubscription-contributor) | EventGrid 이벤트 구독 작업을 관리할 수 있습니다. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription 읽기 권한자](#eventgrid-eventsubscription-reader) | EventGrid 이벤트 구독을 읽을 수 있습니다. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [데이터 기여자](#fhir-data-contributor) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터에 대 한 모든 액세스를 허용 합니다. | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [데이터 내보내기 (& e)](#fhir-data-exporter) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 내보낼 수 있음 | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [데이터 판독기](#fhir-data-reader) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽을 수 있습니다. | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [데이터 기록기](#fhir-data-writer) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 쓸 수 있습니다. | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [통합 서비스 환경 참가자](#integration-service-environment-contributor) | Integration service 환경을 관리할 수 있지만 액세스할 수는 없습니다. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [통합 서비스 환경 개발자](#integration-service-environment-developer) | 개발자가 통합 서비스 환경에서 워크플로, 통합 계정 및 API 연결을 만들고 업데이트할 수 있습니다. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [지능형 시스템 계정 기여자](#intelligent-systems-account-contributor) | 인텔리전트 시스템 계정을 관리할 수 있지만 액세스할 수는 없습니다. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [논리 앱 기여자](#logic-app-contributor) | 논리 앱을 관리할 수 있지만 앱을 변경할 수는 없습니다. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [논리 앱 운영자](#logic-app-operator) | 논리 앱을 읽고 사용하도록 설정하고 사용하지 않도록 설정할 수 있지만 편집하거나 업데이트할 수는 없습니다. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **ID** |  |  |
> | [관리 ID 기여자](#managed-identity-contributor) | 사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다. | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [관리 ID 운영자](#managed-identity-operator) | 사용자 할당 ID를 읽고 할당합니다. | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **보안** |  |  |
> | [Azure Sentinel 기여자](#azure-sentinel-contributor) | Azure Sentinel 기여자 | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel 읽기 권한자](#azure-sentinel-reader) | Azure Sentinel 읽기 권한자 | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel 응답자](#azure-sentinel-responder) | Azure Sentinel 응답자 | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault 관리자 (미리 보기)](#key-vault-administrator-preview) | 인증서, 키 및 비밀을 포함 하 여 주요 자격 증명 모음 및 해당 개체에 있는 모든 개체에 대 한 모든 데이터 평면 작업을 수행 합니다. 주요 자격 증명 모음 리소스를 관리 하거나 역할 할당을 관리할 수 없습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault 인증서 담당자 (미리 보기)](#key-vault-certificates-officer-preview) | 권한 관리를 제외한 key vault의 인증서에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [키 자격 증명 모음 기여자](#key-vault-contributor) | 키 자격 증명 모음을 관리 하지만 Azure RBAC에서 역할을 할당 하는 것을 허용 하지 않으며 비밀, 키 또는 인증서에 액세스할 수 없습니다. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto 담당자 (미리 보기)](#key-vault-crypto-officer-preview) | 권한 관리를 제외한 key vault 키에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault Crypto 서비스 암호화 (미리 보기)](#key-vault-crypto-service-encryption-preview) | 키의 메타 데이터를 읽고 래핑/래핑 해제 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault Crypto 사용자 (미리 보기)](#key-vault-crypto-user-preview) | 키를 사용 하 여 암호화 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault 판독기 (미리 보기)](#key-vault-reader-preview) | 키 자격 증명 모음 및 해당 인증서, 키 및 비밀의 메타 데이터를 읽습니다. 비밀 콘텐츠 또는 키 자료와 같은 중요 한 값을 읽을 수 없습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault 비밀 책임자 (미리 보기)](#key-vault-secrets-officer-preview) | 권한 관리를 제외한 key vault의 비밀에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | b86a8fe4-4948-aee5-eccb2c155cd7 |
> | [Key Vault 비밀 사용자 (미리 보기)](#key-vault-secrets-user-preview) | 비밀 콘텐츠를 읽습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [보안 관리자](#security-admin) | Security Center에 대한 권한을 살펴보고 업데이트할 수 있습니다. 보안 읽기 권한자 역할과 동일한 권한이며, 보안 정책을 업데이트하고 경고 및 권장 사항을 해제할 수도 있습니다. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [보안 평가 기여자](#security-assessment-contributor) | Security Center로 평가를 푸시할 수 있습니다. | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [보안 관리자(레거시)](#security-manager-legacy) | 레거시 역할입니다. 그 대신 보안 관리자를 사용하세요. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [보안 판독기](#security-reader) | Security Center에 대한 권한을 살펴볼 수 있습니다. 권장 사항, 경고, 보안 정책 및 보안 상태를 볼 수 있지만 변경할 수는 없습니다. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Lab 사용자](#devtest-labs-user) | Azure DevTest Labs의 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있습니다. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [랩 작성자](#lab-creator) | Azure 랩 계정으로 새 랩을 만들 수 있습니다. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **모니터** |  |  |
> | [Application Insights 구성 요소 기여자](#application-insights-component-contributor) | Application Insights 구성 요소를 관리할 수 있음 | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights 스냅샷 디버거](#application-insights-snapshot-debugger) | Application Insights 스냅샷 디버거를 사용하여 수집한 디버그 스냅샷을 보고 다운로드할 수 있는 사용자 권한을 제공합니다. 이러한 사용 권한은 [소유자](#owner) 또는 [기여자](#contributor) 역할에 포함되지 않습니다. 사용자에게 Application Insights 스냅샷 디버거 역할을 부여할 때 사용자에게 직접 역할을 부여해야 합니다. 이 역할은 사용자 지정 역할에 추가될 때 인식되지 않습니다. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Monitoring Contributor](#monitoring-contributor) | 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)도 참조하세요. | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [모니터링 메트릭 게시자](#monitoring-metrics-publisher) | Azure 리소스에 대한 게시 메트릭 사용 | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) | 모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)도 참조하세요. | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [통합 문서 기여자](#workbook-contributor) | 공유 통합 문서를 저장할 수 있습니다. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [통합 문서 읽기 권한자](#workbook-reader) | 통합 문서를 읽을 수 있습니다. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **관리 + 거버넌스** |  |  |
> | [Automation 작업 연산자](#automation-job-operator) | Automation Runbook을 사용하여 작업을 만들고 관리합니다. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation 운영자](#automation-operator) | 자동화 연산자는 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있습니다. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook 연산자](#automation-runbook-operator) | Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Connected Machine 온보딩](#azure-connected-machine-onboarding) | Azure Connected Machines을 온보딩할 수 있습니다. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine 리소스 관리자](#azure-connected-machine-resource-administrator) | Azure Connected Machines을 읽고, 쓰고, 삭제하고, 다시 온보딩할 수 있습니다. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [청구 읽기 권한자](#billing-reader) | 결제 데이터에 대해 읽기 권한 허용 | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [청사진 기여자](#blueprint-contributor) | 청사진 정의를 관리할 수 있지만 할당할 수는 없습니다. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [청사진 연산자](#blueprint-operator) | 게시된 기존 청사진을 할당할 수 있지만 새 청사진을 만들 수는 없습니다. 이 역할은 사용자가 할당한 관리 ID를 사용하여 할당하는 경우에만 작동합니다. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management 기여자](#cost-management-contributor) | 비용을 확인하고 비용 구성(예: 예산, 내보내기)을 관리할 수 있음 | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management 읽기 권한자](#cost-management-reader) | 비용 데이터 및 구성(예: 예산, 내보내기)을 확인할 수 있음 | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [계층 구조 설정 관리자](#hierarchy-settings-administrator) | 사용자가 계층 구조 설정을 편집하고 삭제할 수 있습니다. | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes Cluster-Azure Arc 온 보 딩](#kubernetes-cluster---azure-arc-onboarding) | ConnectedClusters 리소스를 만들도록 모든 사용자/서비스에 권한을 부여 하는 역할 정의 | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [관리형 애플리케이션 기여자 역할](#managed-application-contributor-role) | 관리형 애플리케이션 리소스를 만들 수 있습니다. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [관리되는 애플리케이션 운영자 역할](#managed-application-operator-role) | 관리되는 애플리케이션 리소스에서 작업을 읽고 수행할 수 있습니다. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Managed Applications 읽기 권한자](#managed-applications-reader) | 관리 앱 및 요청 JIT 액세스에서 리소스를 읽을 수 있습니다. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [관리형 서비스 등록 할당 삭제 역할](#managed-services-registration-assignment-delete-role) | 관리형 서비스 등록 할당 삭제 역할은 관리하는 테넌트 사용자가 테넌트에 할당된 등록 할당을 삭제할 수 있도록 허용합니다. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [관리 그룹 참가자](#management-group-contributor) | 관리 그룹 참가자 역할 | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [관리 그룹 읽기 권한자](#management-group-reader) | 관리 그룹 읽기 권한자 역할 | ac63b705-f282-497d-ac71-919bf39d939d |
> | [NewRelic APM 계정 기여자](#new-relic-apm-account-contributor) | New Relic Application Performance Management 계정 및 애플리케이션을 관리할 수 있지만 액세스할 수는 없습니다. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights 데이터 쓰기 권한자(미리 보기)](#policy-insights-data-writer-preview) | 리소스 정책에 대한 읽기 액세스 권한과 리소스 구성 요소 정책 이벤트에 대한 쓰기 액세스 권한을 허용합니다. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [리소스 정책 기여자](#resource-policy-contributor) | 리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 사용자입니다. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery 기여자](#site-recovery-contributor) | 자격 증명 모음 만들기 및 역할 할당을 제외한 Site Recovery 서비스를 관리할 수 있습니다. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery 운영자](#site-recovery-operator) | 장애 조치(failover) 및 장애 복구(failback)를 수행할 수 있지만 다른 Site Recovery 관리 작업은 수행할 수 없습니다. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery 구독자](#site-recovery-reader) | Site Recovery 상태를 볼 수 있지만 다른 관리 작업은 수행할 수 없습니다. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [지원 요청 참가자](#support-request-contributor) | 지원 요청을 만들고 관리할 수 있습니다. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [태그 기여자](#tag-contributor) | 엔터티의 태그를 관리할 수 있으며, 엔터티 자체에 대한 액세스 권한은 없습니다. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **기타** |  |  |
> | [BizTalk 참가자](#biztalk-contributor) | BizTalk Services를 관리할 수 있지만 액세스할 수는 없습니다. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [데스크톱 가상화 사용자](#desktop-virtualization-user) | 사용자가 응용 프로그램 그룹에서 응용 프로그램을 사용할 수 있습니다. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Scheduler 작업 컬렉션 참가자](#scheduler-job-collections-contributor) | Scheduler 작업 컬렉션을 관리할 수 있지만 액세스할 수는 없습니다. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>일반


### <a name="contributor"></a>참가자

모든 리소스를 관리할 수 있는 모든 권한을 부여 하지만, Azure RBAC에서 역할을 할당할 수는 없습니다. [자세한 정보](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | * | 모든 유형의 리소스 만들기 및 관리 |
> | **NotActions** |  |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/delete | 역할, 정책 할당, 정책 정의 및 정책 집합 정의를 삭제합니다. |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/*/쓰기 | 역할, 역할 할당, 정책 할당, 정책 정의 및 정책 집합 정의를 기록합니다. |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | 테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한 부여 |
> | [/BlueprintAssignments/write](resource-provider-operations.md#microsoftblueprint) | 청사진 할당을 만들거나 업데이트합니다. |
> | [/BlueprintAssignments/delete](resource-provider-operations.md#microsoftblueprint) | 청사진 할당을 삭제합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>소유자

Azure RBAC에서 역할을 할당 하는 기능을 포함 하 여 모든 리소스를 관리할 수 있는 모든 권한을 부여 합니다. [자세한 정보](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | * | 모든 유형의 리소스 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>판독기

모든 리소스를 볼 수 있지만 변경할 수는 없습니다. [자세한 정보](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>사용자 액세스 관리자

Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. [자세한 정보](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/* | 권한 부여 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>컴퓨팅


### <a name="classic-virtual-machine-contributor"></a>클래식 Virtual Machine 참가자

클래식 가상 머신을 관리할 수 있지만 가상 머신이나 연결된 가상 네트워크 또는 스토리지 계정에 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | 클래식 컴퓨팅 도메인 이름 만들기 및 관리 |
> | [Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | 가상 머신 만들기 및 관리 |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | 예약된 IP를 연결합니다. |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | 예약된 IP를 가져옵니다. |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | 가상 네트워크를 조인합니다. |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | 가상 네트워크를 가져옵니다. |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | 스토리지 계정 디스크를 반환합니다. |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | 스토리지 계정 이미지를 반환합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 지정된 계정의 스토리지 계정을 반환합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>가상 머신 관리자 로그인

포털에서 Virtual Machines를 확인 하 [고 관리자 권한](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md) 으로 로그인 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [/VirtualMachines/*](resource-provider-operations.md#microsoftcompute)/cread |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | /VirtualMachines/login/action [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신에 일반 사용자로 로그인합니다. |
> | /VirtualMachines/loginAsAdmin/action [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신에 Windows 관리자 또는 Linux 루트 사용자 권한으로 로그인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>가상 머신 참가자

가상 머신을 관리할 수 있지만 가상머신이나 연결된 가상 네트워크 또는 스토리지 계정에 액세스할 수는 없습니다. [자세한 정보](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/AvailabilitySets/*](resource-provider-operations.md#microsoftcompute) | 컴퓨팅 가용성 집합 만들기 및 관리 |
> | [/Locations/*](resource-provider-operations.md#microsoftcompute) | 컴퓨팅 위치 만들기 및 관리 |
> | [/VirtualMachines/*](resource-provider-operations.md#microsoftcompute) | 가상 머신 만들기 및 관리 |
> | [/VirtualMachineScaleSets/*](resource-provider-operations.md#microsoftcompute) | 가상 머신 확장 집합 만들기 및 관리 |
> | /Disks/write [계산](resource-provider-operations.md#microsoftcompute) | 새 디스크를 만들거나 기존 디스크를 업데이트합니다. |
> | /Disks/read [계산](resource-provider-operations.md#microsoftcompute) | 디스크의 속성을 가져옵니다. |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | 디스크를 삭제합니다. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | 애플리케이션 게이트웨이 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | 부하 분산 장치 인바운드 NAT 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/locations/* | 네트워크 위치 만들기 및 관리 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | 네트워크 인터페이스 만들기 및 관리 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. 네트워크/네트워크](resource-provider-operations.md#microsoftnetwork)보안 | 네트워크 보안 그룹 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 공용 IP 주소를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/sread |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | 보호 정책을 만듭니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>가상 머신 사용자 로그인

포털에서 Virtual Machines를 보고 일반 사용자 권한으로 로그인합니다. [자세한 정보](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [/VirtualMachines/*](resource-provider-operations.md#microsoftcompute)/cread |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | /VirtualMachines/login/action [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신에 일반 사용자로 로그인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>네트워킹


### <a name="cdn-endpoint-contributor"></a>CD 엔드포인트 참가자

CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN 엔드포인트 독자

CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/읽기 |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN 프로필 참가자

CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. [자세한 정보](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/프로필/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN 프로필 독자

CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/프로/*/> 읽기 |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>클래식 네트워크 참가자

기본 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. [자세한 정보](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | 클래식 네트워크 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS 영역 참가자

Azure DNS의 DNS 영역과 레코드 집합을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. [자세한 정보](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS 영역 및 레코드 만들기 및 관리 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>네트워크 참가자

네트워크를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft 네트워크](resource-provider-operations.md#microsoftnetwork)/* | 네트워크 만들기 및 관리 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>사설 DNS 영역 기여자

개인 DNS 영역 리소스를 관리할 수 있지만 연결 된 가상 네트워크는 관리할 수 없습니다. [자세한 정보](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager 참가자

Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>스토리지


### <a name="avere-contributor"></a>Avere 기여자

Avere vFXT 클러스터를 만들고 관리할 수 있습니다. [자세한 정보](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/*/cread |  |
> | [/AvailabilitySets/*](resource-provider-operations.md#microsoftcompute) |  |
> | [/ProximityPlacementGroups/*](resource-provider-operations.md#microsoftcompute) |  |
> | [/VirtualMachines/*](resource-provider-operations.md#microsoftcompute) |  |
> | [/Disks/*](resource-provider-operations.md#microsoftcompute) |  |
> | [Microsoft. 네트워크](resource-provider-operations.md#microsoftnetwork)/*/읽기 |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 경고할 수 없습니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 저장소](resource-provider-operations.md#microsoftstorage)/*/읽기 |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 스토리지 계정 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [/Subscriptions/resourceGroups/resources/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹에 대한 리소스를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere 운영자

Avere vFXT 클러스터에서 클러스터를 관리 하는 데 사용 됩니다. [자세한 정보](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | /VirtualMachines/read [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신의 속성을 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 경고할 수 없습니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 컨테이너 삭제 결과를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 컨테이너 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Blob 컨테이너 넣기의 결과를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Backup 참가자

백업 서비스를 관리할 수 있지만 자격 증명 모음을 만들고 다른 사용자에 게 액세스 권한을 부여할 수 없습니다. [자세한 정보](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | 백업 관리에 대한 작업의 결과 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Recovery Services 자격 증명 모음의 백업 패브릭 내에서 백업 컨테이너 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | 백업 정책 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | 백업한 항목 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | 백업 항목을 보유하는 컨테이너 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Recovery Services 자격 증명 모음의 백업과 관련된 인증서 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Recovery Services 자격 증명 모음 만들기 및 사용 관리 |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Backup 운영자

백업 제거, 자격 증명 모음 만들기 및 [다른 사용자에 게 액세스](../backup/backup-rbac-rs-vault.md) 권한 부여를 제외 하 고 백업 서비스를 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 보호 항목 Backup을 수행합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 프로비전합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 보호 항목의 복구 지점을 복원합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 취소합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | 서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | 등록된 컨테이너를 만듭니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | 컨테이너 내의 워크로드를 조회합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Backup 읽기 권한자

백업 서비스를 볼 수 있지만 변경할 수 없습니다. [자세한 정보](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | 작업의 작업 결과를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | 모든 작업 개체를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Recovery Services 자격 증명 모음의 Backup 작업 결과를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Recovery Services 자격 증명 모음에 대한 스토리지 구성을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Recovery Services 자격 증명 모음에 구성을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>클래식 Storage 계정 참가자

클래식 Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | 스토리지 계정 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>클래식 스토리지 계정 키 운영자 서비스 역할

클래식 저장소 계정 키 운영자가 클래식 저장소 계정에서 키를 나열 하 고 다시 생성할 수 있습니다. [자세한 정보](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | 스토리지 계정에 대한 기존 액세스 키를 다시 생성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box 기여자

다른 사람에게 액세스 권한을 부여하는 것을 제외한 모든 항목을 Data Box 서비스에서 관리할 수 있습니다. [자세한 정보](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box 읽기 권한자

주문하기나 주문 세부 정보 편집 및 다른 사용자에게 액세스 권한 부여 외에 Data Box 서비스를 관리할 수 있습니다. [자세한 정보](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/*/읽기 |  |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | 주문과 관련된 암호화되지 않은 자격 증명을 나열합니다. |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | 이 메서드는 사용할 수 있는 SKU 목록을 반환합니다. |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | 이 메서드는 모든 유형의 유효성 검사를 수행합니다. |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | 이 메서드는 영역에 대한 구성을 반환합니다. |
> | [Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | 배송 주소의 유효성을 검사하고, 있는 경우, 대체 주소를 제공합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics 개발자

사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. [자세한 정보](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | DataLakeAnalytics 계정을 삭제합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | 다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | DataLakeAnalytics 계정을 만들거나 업데이트합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | 방화벽 규칙을 삭제합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | 컴퓨팅 정책을 만들거나 업데이트합니다. |
> | [DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | 컴퓨팅 정책을 삭제합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>읽기 권한자 및 데이터 액세스

모든 것을 볼 수 있지만, 스토리지 계정 또는 포함된 리소스를 삭제하거나 만들 수는 없습니다. 또한 스토리지 계정 키에 액세스하여 스토리지 계정에 포함된 모든 데이터를 읽고 쓸 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | 지정된 스토리지 계정에 대한 계정 SAS 토큰을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Storage 계정 참가자

스토리지 계정을 관리할 수 있도록 허용합니다. 공유 키 권한 부여를 통해 데이터에 액세스하는 데 사용할 수 있는 계정 키에 대한 액세스 권한을 제공합니다. [자세한 정보](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 스토리지 계정 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>스토리지 계정 키 운영자 서비스 역할

스토리지 계정 액세스 키를 나열하고 다시 생성할 수 있도록 허용합니다. [자세한 정보](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | 지정된 스토리지 계정에 대한 액세스 키를 다시 생성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Storage Blob 데이터 기여자

Azure Storage 컨테이너 및 BLOB을 읽고, 쓰고, 삭제합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 컨테이너를 삭제합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 컨테이너 또는 컨테이너 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | 컨테이너의 메타데이터 또는 속성을 수정합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob을 삭제합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB 또는 BLOB 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | BLOB을 한 경로에서 다른 경로로 이동합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | BLOB에 씁니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Storage Blob 데이터 소유자

POSIX 액세스 제어 할당을 포함하여 Azure Storage BLOB 컨테이너 및 데이터에 대한 모든 액세스 권한을 제공합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | 컨테이너에 대한 모든 권한이 있습니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | BLOB에 대한 모든 권한이 있습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Storage Blob 데이터 읽기 권한자

Azure Storage 컨테이너 및 BLOB을 읽고 나열합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 컨테이너 또는 컨테이너 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB 또는 BLOB 목록을 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage Blob 위임자

Azure AD 자격 증명으로 서명된 컨테이너 또는 BLOB의 공유 액세스 서명을 만드는 데 사용할 수 있는 사용자 위임 키를 가져옵니다. 자세한 내용은 [사용자 위임 SAS 만들기](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)를 참조하세요. [자세한 정보](https://docs.microsoft.com/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Storage 파일 데이터 SMB 공유 기여자

Azure 파일 공유의 파일/디렉터리에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. Windows 파일 서버에는 이 역할에 상응하는 기본 제공 역할이 없습니다. [자세한 정보](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 파일을 쓰거나 폴더를 만든 결과를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 파일/폴더를 삭제한 결과를 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage 파일 데이터 SMB 공유 높은 권한 기여자

Azure 파일 공유의 파일/디렉터리에 대한 ACL을 읽고, 쓰고, 삭제하고, 수정할 수 있습니다. 이 역할은 Windows 파일 서버의 변경 내용에 대한 파일 공유 ACL에 해당합니다. [자세한 정보](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 파일을 쓰거나 폴더를 만든 결과를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 파일/폴더를 삭제한 결과를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 파일/폴더에 대한 권한을 수정한 결과를 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Storage 파일 데이터 SMB 공유 읽기 권한자

Azure 파일 공유의 파일/디렉터리에 대한 읽기 액세스를 허용합니다. 이 역할은 Windows 파일 서버에 대한 파일 공유 ACL 읽기에 해당합니다. [자세한 정보](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Storage 큐 데이터 기여자

Azure Storage 큐 및 큐 메시지를 읽고, 쓰고, 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | 큐를 삭제합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | 큐 메타데이터 또는 속성을 수정합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | 큐에서 하나 이상의 메시지를 삭제합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 큐에서 하나 이상의 메시지를 선택 또는 검색합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | 큐에 메시지를 추가합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Storage 큐 데이터 메시지 처리자

Azure Storage 큐의 메시지를 선택, 검색 및 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 메시지를 선택합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | 메시지를 검색하고 삭제합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Storage 큐 데이터 메시지 보내는 사람

Azure Storage 큐에 메시지를 추가할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | 큐에 메시지를 추가합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Storage 큐 데이터 읽기 권한자

Azure Storage 큐 및 큐 메시지를 읽고 나열할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세한 정보](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 큐에서 하나 이상의 메시지를 선택 또는 검색합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>웹


### <a name="azure-maps-data-reader"></a>Azure Maps 데이터 읽기 권한자

Azure 맵 계정에서 맵 관련 데이터를 읽을 수 있는 액세스 권한을 부여합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/sread |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search 서비스 참가자

Search 서비스를 관리할 수 있지만 액세스할 수는 없습니다. [자세한 정보](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft. 검색/검색](resource-provider-operations.md#microsoftsearch) | 검색 서비스 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>웹 계획 참가자

웹 사이트의 웹 계획을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/serverFarms/* | 서버 팜 만들기 및 관리 |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | App Service Environment를 조인합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>웹 사이트 참가자

웹 사이트(웹 계획은 제외)를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights 구성 요소 만들기 및 관리 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. 웹](resource-provider-operations.md#microsoftweb)/certificates/* | 웹 사이트 인증서 만들기 및 관리 |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트의 이름을 가져옵니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | App Service 계획 조인 |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/sites/* | 웹 사이트 만들기 및 관리(사이트 만들기도 관련 App Service 계획에 대한 쓰기 권한이 필요) |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>컨테이너


### <a name="acrdelete"></a>AcrDelete

acr 삭제 [자세한 정보](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | 컨테이너 레지스트리의 아티팩트를 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr 이미지 서명자 자세히 [알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | 컨테이너 레지스트리에 대한 콘텐츠 신뢰 메타데이터를 푸시/풀합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr 풀 [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr 푸시 [자세한 정보](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | 컨테이너 레지스트리에 이미지를 푸시하거나 작성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

acr 격리 데이터 읽기 권한자

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

acr 격리 데이터 작성자

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | [Microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | 격리된 이미지의 격리 상태를 작성/수정합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service 클러스터 관리자 역할

클러스터 관리자 자격 증명 작업을 나열합니다. [자세한 정보](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | 관리되는 클러스터의 clusterAdmin 자격 증명을 나열합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | 자격 증명 나열을 사용하여 역할 이름별로 관리되는 클러스터 액세스 프로필을 가져옵니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service 클러스터 사용자 역할

클러스터 사용자 자격 증명 작업을 나열합니다. [자세한 정보](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service 기여자 역할

Azure Kubernetes 서비스 클러스터에 대 한 읽기 및 쓰기 권한을 부여 합니다. [자세한 정보](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | 새 관리되는 클러스터를 만들거나 기존 관리되는 클러스터를 업데이트합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes 서비스 RBAC 관리자

리소스 할당량 및 네임 스페이스 업데이트 또는 삭제를 제외 하 고 클러스터/네임 스페이스 아래의 모든 리소스를 관리할 수 있습니다. [자세한 정보](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/write](resource-provider-operations.md#microsoftresources) | 배포를 만들거나 업데이트합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | 다음으로 기록 |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Resourc를 삭제 합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | 네임 스페이스 쓰기 |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | 네임 스페이스 삭제 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes 서비스 RBAC 클러스터 관리자

클러스터의 모든 리소스를 관리할 수 있습니다. [자세한 정보](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/write](resource-provider-operations.md#microsoftresources) | 배포를 만들거나 업데이트합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes 서비스 RBAC 판독기

암호를 제외한 클러스터/네임 스페이스의 모든 리소스를 볼 수 있습니다. [자세한 정보](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/write](resource-provider-operations.md#microsoftresources) | 배포를 만들거나 업데이트합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/읽기 |  |
> | **NotDataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/읽기 |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/쓰기 |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/secrets/*"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes 서비스 RBAC 기록기

리소스 할당량, 네임 스페이스, pod 보안 정책, 인증서 서명 요청, (클러스터) 역할 및 (클러스터) 역할 바인딩을 제외 하 고 클러스터/네임 스페이스의 모든 항목을 업데이트할 수 있습니다. [자세한 정보](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/write](resource-provider-operations.md#microsoftresources) | 배포를 만들거나 업데이트합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/읽기 |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/쓰기 |  |
> | **NotDataActions** |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/읽기 |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/쓰기 |  |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | 네임 스페이스 쓰기 |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | 다음으로 기록 |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/certificates.k8s.io/certificatesigningrequests/write | Certificatesigningrequests 쓰기 |
> | [ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/podsecuritypolicies/write | Podsecuritypolicies 쓰기 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except resource quotas, namespaces, pod security policies, certificate signing requests, (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read",
        "Microsoft.ContainerService/managedClusters/*/write"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/write",
        "Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/write"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>데이터베이스


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB 계정 독자 역할

Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. [자세한 정보](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/읽기 | 컬렉션 읽기 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | 메트릭 읽기 |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB 운영자

Azure Cosmos DB 계정을 관리할 수 있지만 계정의 데이터에 액세스할 수는 없습니다. 계정 키 및 연결 문자열에 대한 액세스를 차단합니다. [자세한 정보](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

계정에 대 한 Cosmos DB 데이터베이스 또는 컨테이너에 대해 복원 요청을 제출할 수 있습니다. [자세한 정보](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | 백업 구성하는 요청 제출 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | 복원 요청 제출 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB 계정 참가자

Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. [자세한 정보](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Azure Cosmos DB 계정 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache 참가자

Redis Cache를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/Register/action](resource-provider-operations.md#microsoftcache) | ‘Microsoft.Cache’ 리소스 공급자를 구독에 등록합니다. |
> | [/Redis/*](resource-provider-operations.md#microsoftcache) | Redis 캐시 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB 참가자

SQL 데이터베이스를 관리할 수 있지만 액세스할 수는 없습니다. 또한 보안 관련 정책이나 부모 SQL 서버를 관리할 수 없습니다. [자세한 정보](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/locations/*/읽기 |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | SQL 데이터베이스 만들기 및 관리 |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingPolicies/* | 감사 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | 감사 설정 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/connectionPolicies/* | 연결 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | 데이터 마스킹 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | 보안 경고 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | 보안 메트릭 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance 기여자

SQL Managed Instances 및 필수 네트워크 구성을 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Pgsecurityggggggg/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/locations/*/읽기 |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL 보안 관리자

SQL Server 및 데이터베이스의 보안과 관련된 정책을 관리할 수 있지만 여기에 액세스할 수는 없습니다. [자세한 정보](../sql-database/sql-database-advanced-data-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/auditingPolicies/* | SQL 서버 감사 정책 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL 서버 감사 설정 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | 지정된 서버에 구성된 확장 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | 지정된 데이터베이스에 구성된 확장 Blob 감사 정책의 세부 정보를 검색합니다. |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | 데이터베이스 목록을 가져오거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | 데이터베이스 스키마를 가져옵니다. |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | 데이터베이스 열을 가져옵니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | 데이터베이스 테이블을 가져옵니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 만들기 및 관리 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server 참가자

SQL Server 및 데이터베이스를 관리할 수 있지만 액세스할 수는 없으며, 해당하는 보안 관련 정책에도 액세스할 수 없습니다. [자세한 정보](../sql-database/sql-database-aad-authentication-configure.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/locations/*/읽기 |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/* | SQL 서버 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/auditingPolicies/* | SQL 서버 감사 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL 서버 감사 설정 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 편집 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>분석


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs 데이터 소유자

Azure Event Hubs 리소스에 대한 전체 액세스를 허용합니다. [자세한 정보](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs 데이터 받는 사람

Azure Event Hubs 리소스에 대한 받기 액세스 권한을 허용합니다. [자세한 정보](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs 데이터 보내는 사람

Azure Event Hubs 리소스에 대한 보내기 액세스 권한을 허용합니다. [자세한 정보](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory 참가자

데이터 팩터리를 만들고 관리하며 해당 하위 리소스도 만들고 관리합니다. [자세한 정보](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
> | [DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | eventSubscription을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>데이터 제거자

분석 데이터를 제거할 수 있습니다. [자세히 알아보세요](../azure-monitor/platform/personal-data-mgmt.md) .

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/*/읽기 |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Application Insights에서 데이터 삭제 |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/읽기 | 로그 분석 데이터를 봅니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | 작업 영역에서 지정된 데이터를 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight 클러스터 운영자

HDInsight 클러스터 구성을 읽고 수정할 수 있습니다. [자세한 정보](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/*/읽기 |  |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | HDInsight 클러스터에 대한 게이트웨이 설정을 가져옵니다. |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | HDInsight 클러스터에 대한 게이트웨이 설정을 업데이트합니다. |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [/Deployments/operations/read](resource-provider-operations.md#microsoftresources) | 배포 작업을 가져오거나 나열합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight 도메인 서비스 기여자

HDInsight에 필요한 도메인 서비스 관련 작업을 읽고, 만들고, 수정 하 고, 삭제할 수 Enterprise Security Package [자세한 정보](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [MICROSOFT AAD](resource-provider-operations.md#microsoftaad)/*/읽기 |  |
> | [/DomainServices/*](resource-provider-operations.md#microsoftaad)/읽기 |  |
> | [/DomainServices/oucontainer/*](resource-provider-operations.md#microsoftaad) |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics 참가자

Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. [자세한 정보](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [/AutomationAccounts/*](resource-provider-operations.md#microsoftautomation) |  |
> | [Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [/VirtualMachines/extensions/*](resource-provider-operations.md#microsoftcompute) |  |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Azure Arc 확장을 설치 또는 업데이트합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourcegroups/deployments/*](resource-provider-operations.md#microsoftresources) |  |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics 독자

Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. [자세한 정보](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>블록체인


### <a name="blockchain-member-node-access-preview"></a>블록체인 멤버 노드 액세스(미리 보기)

블록 체인 [멤버 노드에 대](../blockchain/service/configure-aad.md) 한 액세스를 허용 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | 기존 블록체인 멤버 트랜잭션 노드를 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | 블록체인 멤버 트랜잭션 노드에 연결합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + 기계 학습


### <a name="cognitive-services-contributor"></a>Cognitive Services 기여자

Cognitive Services의 키를 만들고, 읽고, 업데이트하고, 삭제 및 관리할 수 있습니다. [자세한 정보](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Cognitiveservices account](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [/Features/read](resource-provider-operations.md#microsoftfeatures) | 구독 기능을 가져옵니다. |
> | [/Providers/features/read](resource-provider-operations.md#microsoftfeatures) | 지정된 리소스 공급자에서 구독의 기능을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 로그 정의 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Deployments/operations/read](resource-provider-operations.md#microsoftresources) | 배포 작업을 가져오거나 나열합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourcegroups/deployments/*](resource-provider-operations.md#microsoftresources) |  |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services 데이터 읽기 권한자(미리 보기)

Cognitive Services 데이터를 읽을 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Cognitiveservices account](resource-provider-operations.md#microsoftcognitiveservices)/*/읽기 |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services 사용자

Cognitive Services의 키를 읽고 나열할 수 있습니다. [자세한 정보](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Cognitiveservices account](resource-provider-operations.md#microsoftcognitiveservices)/*/읽기 |  |
> | [Cognitiveservices account](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | 키를 나열합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | 리소스 진단 설정을 읽습니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 로그 정의 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/operations/read](resource-provider-operations.md#microsoftresources) | 배포 작업을 가져오거나 나열합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Cognitiveservices account](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>혼합 현실


### <a name="remote-rendering-administrator"></a>원격 렌더링 관리자

Azure 원격 렌더링을 위한 변환, 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다. [자세한 정보](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | 자산 변환 시작 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | 자산 변환 속성 가져오기 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | 자산 변환 중지 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 세션 속성 가져오기 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 세션 시작 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 세션 중지 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 세션에 연결 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | 원격 렌더링 검사자에 연결 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>원격 렌더링 클라이언트

Azure 원격 렌더링을 위한 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다. [자세한 정보](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 세션 속성 가져오기 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 세션 시작 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 세션 중지 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 세션에 연결 |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | 원격 렌더링 검사자에 연결 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors 계정 기여자

계정의 공간 앵커를 관리할 수 있지만 삭제할 수는 없습니다. [Learn more](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 공간 앵커를 만듭니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 주변의 공간 앵커를 검색합니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors 서비스 품질 개선에 도움이 되는 진단 데이터를 제출합니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 공간 앵커 속성을 업데이트합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors 계정 소유자

계정에서 공간 앵커를 관리할 수 있습니다. [자세한 정보](../spatial-anchors/concepts/authentication.md) 를 삭제 하는 것을 포함 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 공간 앵커를 만듭니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | 공간 앵커를 삭제합니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 주변의 공간 앵커를 검색합니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors 서비스 품질 개선에 도움이 되는 진단 데이터를 제출합니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 공간 앵커 속성을 업데이트합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors 계정 읽기 권한자

계정에서 공간 앵커의 속성을 찾고 읽을 수 있습니다. [자세히 알아보세요](../spatial-anchors/concepts/authentication.md) .

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 주변의 공간 앵커를 검색합니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다. |
> | [MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors 서비스 품질 개선에 도움이 되는 진단 데이터를 제출합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>통합


### <a name="api-management-service-contributor"></a>API Management 서비스 참가자

서비스 및 Api를 관리할 수 있습니다. [자세한 정보](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/* | API Management 서비스 만들기 및 관리 |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management 서비스 운영자 역할

서비스를 관리할 수 있지만 Api는 관리할 수 없습니다. [자세한 정보](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | API Management 서비스 인스턴스 읽기 |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다. |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | API Management 서비스 인스턴스를 삭제합니다. |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다. |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/sv> 읽기 | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너에서 API Management 서비스 복원 |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | API Management 서비스에 대한 TLS/SSL 인증서를 업로드합니다. |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다. |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/write | API Management 서비스 인스턴스를 만들거나 업데이트합니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 사용자와 연결된 키를 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Azure API Management 읽기 권한자 역할

서비스 및 Api에 대 한 읽기 전용 액세스 [자세한 정보](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | API Management 서비스 인스턴스 읽기 |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/sv> 읽기 | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.apimanagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 사용자와 연결된 키를 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>App Configuration 데이터 소유자

App Configuration 데이터에 대한 모든 액세스 권한을 허용합니다. [자세한 정보](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/읽기 |  |
> | [Microsoft AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>App Configuration 데이터 읽기 권한자

App Configuration 데이터에 대한 읽기 액세스 권한을 허용합니다. [자세한 정보](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/읽기 |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus 데이터 소유자

Azure Service Bus 리소스에 대한 전체 액세스를 허용합니다. [자세한 정보](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus 데이터 받는 사람

Azure Service Bus 리소스에 대한 받기 액세스 권한을 허용합니다. [자세한 정보](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/항목/읽기 |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus 데이터 보내는 사람

Azure Service Bus 리소스에 대한 보내기 액세스 권한을 허용합니다. [자세한 정보](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/항목/읽기 |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack 등록 소유자

Azure Stack 등록을 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Azure Stack Edge 구독의 속성을 가져옵니다. |
> | [Microsoft AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Azure Stack Marketplace 제품의 속성 가져오기 |
> | [Microsoft AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Azure Stack 등록의 속성 가져오기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 기여자

EventGrid 이벤트 구독 작업을 관리할 수 있습니다. [자세한 정보](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 항목 유형별로 글로벌 이벤트 구독을 나열합니다. |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 읽기 권한자

EventGrid 이벤트 구독을 읽을 수 있습니다. [자세한 정보](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | eventSubscription을 읽습니다. |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 항목 유형별로 글로벌 이벤트 구독을 나열합니다. |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | [Microsoft EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>데이터 기여자

역할을 통해 사용자 또는 보안 주체에 게 FHIR 데이터에 대 한 모든 권한을 허용 합니다. [자세한 정보](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | HealthcareApis/서비스/a r/리소스/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>데이터 내보내기 (& e)

역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 내보낼 수 있습니다. [자세한 정보](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | HealthcareApis/서비스/a r/리소스/읽기 | 검색 및 버전이 지정 된 기록이 포함 된 FHIR 리소스를 읽습니다.  |
> | HealthcareApis/서비스/a r/리소스/내보내기/작업 | 내보내기 작업 ($export) |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>데이터 판독기

역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽을 수 있습니다. [자세한 정보](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | HealthcareApis/서비스/a r/리소스/읽기 | 검색 및 버전이 지정 된 기록이 포함 된 FHIR 리소스를 읽습니다.  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>데이터 기록기

역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 쓸 수 있습니다. [자세한 정보](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | HealthcareApis/서비스/a r/리소스/* |  |
> | **NotDataActions** |  |
> | HealthcareApis/services/fhir/resources/하드 삭제/작업 | 하드 삭제 (버전 기록 포함) |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>통합 서비스 환경 참가자

Integration service 환경을 관리할 수 있지만 액세스할 수는 없습니다. [자세한 정보](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [/IntegrationServiceEnvironments/*](resource-provider-operations.md#microsoftlogic) |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>통합 서비스 환경 개발자

개발자가 통합 서비스 환경에서 워크플로, 통합 계정 및 API 연결을 만들고 업데이트할 수 있습니다. [자세한 정보](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [/IntegrationServiceEnvironments/read](resource-provider-operations.md#microsoftlogic) | 통합 서비스 환경을 읽습니다. |
> | [/IntegrationServiceEnvironments/join/action](resource-provider-operations.md#microsoftlogic) | 통합 서비스 환경에 조인합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>지능형 시스템 계정 참가자

인텔리전트 시스템 계정을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | Microsoft.IntelligentSystems/accounts/* | 지능형 시스템 계정 만들기 및 관리 |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>논리 앱 참가자

논리 앱을 관리할 수 있지만 앱을 변경할 수는 없습니다. [자세한 정보](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 지정된 계정의 스토리지 계정을 반환합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | 이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
> | [Microsoft 논리](resource-provider-operations.md#microsoftlogic)/* | Logic Apps 리소스를 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/connectionGateways/* | 연결 게이트웨이를 만들고 관리합니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/connections/* | 연결을 만들고 관리합니다. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Customapis/* | 사용자 지정 API를 만들고 관리합니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | App Service 계획 조인 |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | 함수 비밀을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>논리 앱 운영자

논리 앱을 읽고 사용하도록 설정하고 사용하지 않도록 설정할 수 있지만 편집하거나 업데이트할 수는 없습니다. [자세한 정보](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/읽기 | Insights 경고 규칙을 읽습니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/읽기 |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/읽기 | Logic Apps에 대한 진단 설정을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/읽기 | Logic Apps에 사용 가능한 메트릭을 가져옵니다. |
> | [Microsoft 논리](resource-provider-operations.md#microsoftlogic)/*/읽기 | Logic Apps 리소스를 읽습니다. |
> | [/Workflows/disable/action](resource-provider-operations.md#microsoftlogic) | 워크플로를 사용하지 않도록 설정합니다. |
> | [/Workflows/enable/action](resource-provider-operations.md#microsoftlogic) | 워크플로를 사용하도록 설정합니다. |
> | [/Workflows/validate/action](resource-provider-operations.md#microsoftlogic) | 워크플로의 유효성을 검사합니다. |
> | [/Deployments/operations/read](resource-provider-operations.md#microsoftresources) | 배포 작업을 가져오거나 나열합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/connectionGateways/*/읽기 | 연결 게이트웨이를 읽습니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/connections/*/읽기 | 연결을 읽습니다. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Customapis/*/sread | 사용자 지정 API를 읽습니다. |
> | [Microsoft 웹](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>ID


### <a name="managed-identity-contributor"></a>관리 ID 참가자

사용자 할당 Id 만들기, 읽기, 업데이트 및 삭제 [자세한 정보](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.managedidentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | 기존 사용자 할당 ID를 가져옵니다. |
> | [Microsoft.managedidentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | 새로운 사용자 할당 ID를 만들거나 기존 사용자 할당 ID와 연결된 태그를 업데이트합니다. |
> | [Microsoft.managedidentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | 기존 사용자 할당 ID를 삭제합니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>관리 ID 운영자

사용자 할당 Id를 읽고 할당 합니다. [자세한 정보](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.managedidentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/읽기 |  |
> | [Microsoft.managedidentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>보안


### <a name="azure-sentinel-contributor"></a>Azure Sentinel 기여자

Azure 센티널 참여자 [자세히 알아보기](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/읽기 | 로그 분석 데이터를 봅니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/읽기 |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel 읽기 권한자

Azure 센티널 판독기 [자세히 알아보기](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/읽기 |  |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 사용자 권한 부여 및 라이선스를 확인합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/읽기 | 로그 분석 데이터를 봅니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | 지정된 작업 영역에서 연결된 서비스를 가져옵니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 저장된 검색 쿼리를 가져옵니다. |
> | [Microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/읽기 |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel 응답자

Azure 센티널 응답기 [자세히 알아보기](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/읽기 |  |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 사용자 권한 부여 및 라이선스를 확인합니다. |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/읽기 | 로그 분석 데이터를 봅니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 저장된 검색 쿼리를 가져옵니다. |
> | [Microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/읽기 |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator-preview"></a>Key Vault 관리자 (미리 보기)

인증서, 키 및 비밀을 포함 하 여 주요 자격 증명 모음 및 해당 개체에 있는 모든 개체에 대 한 모든 데이터 평면 작업을 수행 합니다. 주요 자격 증명 모음 리소스를 관리 하거나 역할 할당을 관리할 수 없습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform any action on certificates, keys and secrets of a key vault, except manage permissions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Key Vault 인증서 담당자 (미리 보기)

권한 관리를 제외한 key vault의 인증서에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform any action on the certificates of a key vault, except manage permissions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault 참가자

키 자격 증명 모음을 관리 하지만 Azure RBAC에서 역할을 할당 하는 것을 허용 하지 않으며 비밀, 키 또는 인증서에 액세스할 수 없습니다. [자세한 정보](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | 일시 삭제된 Key Vault를 제거합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer-preview"></a>Key Vault Crypto 담당자 (미리 보기)

권한 관리를 제외한 key vault 키에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform any action on the keys of a key vault, except manage permissions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-preview"></a>Key Vault Crypto 서비스 암호화 (미리 보기)

키의 메타 데이터를 읽고 래핑/래핑 해제 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 지정 된 자격 증명 모음에 키를 나열 하거나 키의 속성 및 공개 자료를 읽습니다. 비대칭 키의 경우이 작업은 공개 키를 노출 하 고 서명 암호화 및 확인 같은 공개 키 알고리즘을 수행 하는 기능을 포함 합니다. 개인 키 및 대칭 키는 노출 되지 않습니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Key Vault 키를 사용 하 여 대칭 키를 래핑합니다. Key Vault 키가 비대칭 인 경우 읽기 액세스를 사용 하 여이 작업을 수행할 수 있습니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Key Vault 키를 사용 하 여 대칭 키를 래핑 해제 합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read metadata of keys and perform wrap/unwrap operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault Crypto 사용자 (미리 보기)

키를 사용 하 여 암호화 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 지정 된 자격 증명 모음에 키를 나열 하거나 키의 속성 및 공개 자료를 읽습니다. 비대칭 키의 경우이 작업은 공개 키를 노출 하 고 서명 암호화 및 확인 같은 공개 키 알고리즘을 수행 하는 기능을 포함 합니다. 개인 키 및 대칭 키는 노출 되지 않습니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | 지정 된 키와 연결 된 지정 된 특성을 업데이트 합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | 키의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 Key Vault에서 키를 복원 하는 데 사용할 수 있습니다. 제한이 적용 될 수 있습니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | 키를 사용 하 여 일반 텍스트를 암호화 합니다. 키가 비대칭 이면 읽기 권한이 있는 보안 주체가이 작업을 수행할 수 있습니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | 키를 사용 하 여 암호 해독 암호를 해독 합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Key Vault 키를 사용 하 여 대칭 키를 래핑합니다. Key Vault 키가 비대칭 인 경우 읽기 액세스를 사용 하 여이 작업을 수행할 수 있습니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Key Vault 키를 사용 하 여 대칭 키를 래핑 해제 합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | 키를 사용 하 여 해시에 서명 합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | 해시를 확인 합니다. 키가 비대칭 이면 읽기 권한이 있는 보안 주체가이 작업을 수행할 수 있습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform cryptographic operations on keys and certificates.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault 판독기 (미리 보기)

키 자격 증명 모음 및 해당 인증서, 키 및 비밀의 메타 데이터를 읽습니다. 비밀 콘텐츠 또는 키 자료와 같은 중요 한 값을 읽을 수 없습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 암호의 속성을 나열 하거나 표시 하지만 해당 값은 표시 하지 않습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read metadata of key vaults and its certificates, keys and secrets. Cannot read sensitive values such as secret contents or key material.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Key Vault 비밀 책임자 (미리 보기)

권한 관리를 제외한 key vault의 비밀에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/sread |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform any action on the secrets of a key vault, except manage permissions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Key Vault 비밀 사용자 (미리 보기)

비밀 콘텐츠를 읽습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | 비밀 값을 가져옵니다. |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 암호의 속성을 나열 하거나 표시 하지만 해당 값은 표시 하지 않습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read secret contents.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>보안 관리자

Security Center에 대한 권한을 살펴보고 업데이트할 수 있습니다. 보안 읽기 권한자 역할과 동일한 권한이며, 보안 정책을 업데이트하고 경고 및 권장 사항을 해제할 수도 있습니다. [자세한 정보](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | 정책 할당 만들기 및 관리 |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | 정책 정의 만들기 및 관리 |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | 정책 집합 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/읽기 | 로그 분석 데이터를 봅니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 보안](resource-provider-operations.md#microsoftsecurity)/* | 보안 구성 요소 및 정책 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>보안 평가 기여자

Security Center로 평가를 푸시할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | 구독에 대한 보안 평가를 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>보안 관리자(레거시)

레거시 역할입니다. 그 대신 보안 관리자를 사용하세요.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/*/읽기 | 클래식 가상 머신에 대한 구성 정보 읽기 |
> | [Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/쓰기 | 클래식 가상 머신에 대한 구성 정보 쓰기 |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/읽기 | 클래식 네트워크에 대한 구성 정보 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 보안](resource-provider-operations.md#microsoftsecurity)/* | 보안 구성 요소 및 정책 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>보안 판독기

Security Center에 대한 권한을 살펴볼 수 있습니다. 권장 사항, 경고, 보안 정책 및 보안 상태를 볼 수 있지만 변경할 수는 없습니다. [자세한 정보](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/읽기 | 로그 분석 데이터를 봅니다. |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/*/읽기 |  |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft. 보안](resource-provider-operations.md#microsoftsecurity)/*/읽기 | 보안 구성 요소 및 정책 읽기 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/*/읽기 |  |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Lab 사용자

Azure DevTest Labs의 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있습니다. [자세한 정보](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | /AvailabilitySets/read [계산](resource-provider-operations.md#microsoftcompute) | 가용성 집합의 속성을 가져옵니다. |
> | [/VirtualMachines/*](resource-provider-operations.md#microsoftcompute)/cread | 가상 머신(VM 크기, 런타임 상태, VM 확장 등)의 속성 읽기 |
> | /VirtualMachines/deallocate/action [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
> | /VirtualMachines/read [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신의 속성을 가져옵니다. |
> | /VirtualMachines/restart/action [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신을 다시 시작합니다. |
> | /VirtualMachines/start/action [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신을 시작합니다. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/읽기 | 랩의 속성 읽기 |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | 랩에서 임의 클레임 가능 가상 머신을 클레임합니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | 랩에 가상 머신을 만듭니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | 현재 사용자가 랩에서 유효한 프로필을 갖도록 관리합니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | 수식을 삭제합니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | 수식을 읽습니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | 수식을 추가하거나 수정합니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | 랩 정책을 평가합니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | 기존 가상 머신의 소유권을 가져옵니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> | [Microsoft DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | 가상 머신에 대한 RDP 파일의 콘텐츠를 나타내는 문자열을 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | 네트워크 인터페이스(예: 네트워크 인터페이스의 일부인 모든 부하 분산 장치)의 속성 읽기 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Virtual Machine을 네트워크 인터페이스에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | 공용 IP 주소의 속성 읽기 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 공용 IP 주소를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [/Deployments/operations/read](resource-provider-operations.md#microsoftresources) | 배포 작업을 가져오거나 나열합니다. |
> | [/Deployments/read](resource-provider-operations.md#microsoftresources) | 배포를 가져오거나 나열합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | **NotActions** |  |
> | /VirtualMachines/vmSizes/read [계산](resource-provider-operations.md#microsoftcompute) | 가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>랩 작성자

Azure 랩 계정으로 새 랩을 만들 수 있습니다. [자세한 정보](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/읽기 |  |
> | [Microsoft/LabAccounts/createLab/action 서비스](resource-provider-operations.md#microsoftlabservices) | 랩 계정에서 랩을 만듭니다. |
> | [Microsoft/LabAccounts/getPricingAndAvailability/action 서비스](resource-provider-operations.md#microsoftlabservices) | 랩 계정에 대한 크기, 지역 및 운영 체제 조합의 가격 책정 및 가용성을 가져옵니다. |
> | [Microsoft/LabAccounts/getRestrictionsAndUsage/action 서비스](resource-provider-operations.md#microsoftlabservices) | 이 구독에 대한 주요 제한 및 사용 정보를 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>모니터


### <a name="application-insights-component-contributor"></a>Application Insights 구성 요소 참가자

Application Insights 구성 요소를 관리할 수 있습니다. [자세한 정보](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 경고 규칙을 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | 새 경고 규칙을 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights 구성 요소 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights 웹 테스트를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights 스냅샷 디버거

Application Insights 스냅샷 디버거를 사용하여 수집한 디버그 스냅샷을 보고 다운로드할 수 있는 사용자 권한을 제공합니다. 이러한 사용 권한은 [소유자](#owner) 또는 [기여자](#contributor) 역할에 포함되지 않습니다. 사용자에게 Application Insights 스냅샷 디버거 역할을 부여할 때 사용자에게 직접 역할을 부여해야 합니다. 이 역할은 사용자 지정 역할에 추가될 때 인식되지 않습니다. [자세한 정보](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/*/읽기 |  |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Monitoring Contributor

모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)도 참조하세요. [자세한 정보](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/actiong/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights 구성 요소 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | 구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | 이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | 리소스에 대한 메트릭을 읽습니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights 웹 테스트를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | 새 작업 영역을 만들거나 기존 작업 영역의 고객 ID를 제공하여 기존 작업 영역에 연결합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | 로그 분석 솔루션 팩을 읽고 쓰고 삭제합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | 로그 분석의 저장된 검색을 읽고 쓰고 삭제합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | 로그 분석 스토리지 인사이트 구성을 읽고 쓰고 삭제합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* |  |
> | [WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/Notificationsettings/* |  |
> | [AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionrules/* |  |
> | [AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Smartgroups//* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>모니터링 메트릭 게시자

Azure 리소스에 대 한 메트릭 게시를 사용 하도록 설정 합니다. [자세한 정보](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | 메트릭을 작성합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Monitoring Reader

모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)도 참조하세요. [자세한 정보](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>통합 문서 기여자

공유 통합 문서를 저장할 수 있습니다. [자세한 정보](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | 통합 문서를 만들거나 업데이트합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/통합 | 통합 문서 삭제 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>통합 문서 읽기 권한자

통합 문서를 읽을 수 있습니다. [자세한 정보](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [microsoft insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>관리 + 거버넌스


### <a name="automation-job-operator"></a>Automation 작업 연산자

Automation Runbook을 사용하여 작업을 만들고 관리합니다. [자세한 정보](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/AutomationAccounts/hybridRunbookWorkerGroups/read](resource-provider-operations.md#microsoftautomation) | Hybrid Runbook Worker 리소스를 읽습니다. |
> | [/AutomationAccounts/jobs/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 가져옵니다. |
> | [/AutomationAccounts/jobs/resume/action](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 계속합니다. |
> | [/AutomationAccounts/jobs/stop/action](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 중지합니다. |
> | [/AutomationAccounts/jobs/streams/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업 스트림을 가져옵니다. |
> | [/AutomationAccounts/jobs/suspend/action](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 일시 중단합니다. |
> | [/AutomationAccounts/jobs/write](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 만듭니다. |
> | [/AutomationAccounts/jobs/output/read](resource-provider-operations.md#microsoftautomation) | 작업의 출력을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation 운영자

Automation 연산자는 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있습니다. [자세한 정보](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/AutomationAccounts/hybridRunbookWorkerGroups/read](resource-provider-operations.md#microsoftautomation) | Hybrid Runbook Worker 리소스를 읽습니다. |
> | [/AutomationAccounts/jobs/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 가져옵니다. |
> | [/AutomationAccounts/jobs/resume/action](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 계속합니다. |
> | [/AutomationAccounts/jobs/stop/action](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 중지합니다. |
> | [/AutomationAccounts/jobs/streams/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업 스트림을 가져옵니다. |
> | [/AutomationAccounts/jobs/suspend/action](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 일시 중단합니다. |
> | [/AutomationAccounts/jobs/write](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업을 만듭니다. |
> | [/AutomationAccounts/jobSchedules/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업 일정을 가져옵니다. |
> | [/AutomationAccounts/jobSchedules/write](resource-provider-operations.md#microsoftautomation) | Azure Automation 작업 일정을 만듭니다. |
> | [/AutomationAccounts/linkedWorkspace/read](resource-provider-operations.md#microsoftautomation) | 자동화 계정에 연결된 작업 영역 가져오기 |
> | [/AutomationAccounts/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 계정을 가져옵니다. |
> | [/AutomationAccounts/runbooks/read](resource-provider-operations.md#microsoftautomation) | Azure Automation Runbook을 가져옵니다. |
> | [/AutomationAccounts/schedules/read](resource-provider-operations.md#microsoftautomation) | Azure Automation 일정 자산을 가져옵니다. |
> | [/AutomationAccounts/schedules/write](resource-provider-operations.md#microsoftautomation) | Azure Automation 일정 자산을 만들거나 업데이트합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/AutomationAccounts/jobs/output/read](resource-provider-operations.md#microsoftautomation) | 작업의 출력을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook 연산자

Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. [자세한 정보](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/AutomationAccounts/runbooks/read](resource-provider-operations.md#microsoftautomation) | Azure Automation Runbook을 가져옵니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine 온보딩

Azure Connected Machines을 온보딩할 수 있습니다. [자세한 정보](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Azure Arc 머신을 읽습니다. |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Azure Arc 머신을 씁니다. |
> | [GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | 게스트 구성 할당을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine 리소스 관리자

Azure Connected Machines을 읽고, 쓰고, 삭제하고, 다시 온보딩할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Azure Arc 머신을 읽습니다. |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Azure Arc 머신을 씁니다. |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Azure Arc 머신을 삭제합니다. |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/reconnect/action | Azure Arc 머신을 다시 연결합니다. |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Azure Arc 확장을 설치 또는 업데이트합니다. |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/읽기 |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>청구 읽기 권한자

청구 데이터에 대 한 읽기 액세스를 허용 합니다. [자세한 정보](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft. 요금 청구](resource-provider-operations.md#microsoftbilling)/*/읽기 | 대금 청구 정보 읽기 |
> | [Microsoft 상거래](resource-provider-operations.md#microsoftcommerce)/*/읽기 |  |
> | [Microsoft 소비량](resource-provider-operations.md#microsoftconsumption)/*/읽기 |  |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/읽기 |  |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>청사진 기여자

청사진 정의를 관리할 수 있지만 할당할 수는 없습니다. [자세한 정보](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft. 청사진](resource-provider-operations.md#microsoftblueprint)/blueprints/* | 청사진 정의 또는 청사진 아티팩트를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>청사진 운영자

게시된 기존 청사진을 할당할 수 있지만 새 청사진을 만들 수는 없습니다. 이 역할은 사용자가 할당한 관리 ID를 사용하여 할당하는 경우에만 작동합니다. [자세한 정보](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft. 청사진](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | 청사진 할당을 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management 기여자

비용을 보고 비용 구성을 관리할 수 있습니다 (예: 예산, 내보내기) [자세한 정보](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 소비량](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft. 청구](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 구성 가져오기 |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 권장 사항을 읽습니다. |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management 읽기 권한자

비용 데이터 및 구성 (예: 예산, 내보내기)을 볼 수 있습니다. [자세한 정보](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 소비량](resource-provider-operations.md#microsoftconsumption)/*/읽기 |  |
> | [CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/읽기 |  |
> | [Microsoft. 청구](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 구성 가져오기 |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 권장 사항을 읽습니다. |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>계층 구조 설정 관리자

사용자가 계층 구조 설정을 편집하고 삭제할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement) | 관리 그룹 계층 구조 설정을 만들거나 업데이트합니다. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementg/sv/delete | 관리 그룹 계층 구조 설정을 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes Cluster-Azure Arc 온 보 딩

ConnectedClusters 리소스를 만들도록 모든 사용자/서비스에 권한을 부여 하는 역할 정의 [자세히 알아보기](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [/Deployments/write](resource-provider-operations.md#microsoftresources) | 배포를 만들거나 업데이트합니다. |
> | [/Subscriptions/operationresults/read](resource-provider-operations.md#microsoftresources) | 구독 작업 결과를 가져옵니다. |
> | [/Subscriptions/read](resource-provider-operations.md#microsoftresources) | 구독 목록을 가져옵니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | ConnectedClusters 쓰기 |
> | [Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | ConnectedClusters 읽기 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>관리형 애플리케이션 기여자 역할

관리형 애플리케이션 리소스를 만들 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [/Applications/*](resource-provider-operations.md#microsoftsolutions) |  |
> | [/Register/action](resource-provider-operations.md#microsoftsolutions) | 솔루션에 등록합니다. |
> | [/Subscriptions/resourceGroups/*](resource-provider-operations.md#microsoftresources) |  |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>관리되는 애플리케이션 운영자 역할

관리되는 애플리케이션 리소스에서 작업을 읽고 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [/Applications/read](resource-provider-operations.md#microsoftsolutions) | 애플리케이션 목록을 검색합니다. |
> | [Microsoft Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Managed Applications 읽기 권한자

관리 앱 및 요청 JIT 액세스에서 리소스를 읽을 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/JitRequests/*](resource-provider-operations.md#microsoftsolutions) |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>관리형 서비스 등록 할당 삭제 역할

관리형 서비스 등록 할당 삭제 역할은 관리하는 테넌트 사용자가 테넌트에 할당된 등록 할당을 삭제할 수 있도록 허용합니다. [자세한 정보](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | 관리형 서비스 등록 할당 목록을 검색합니다. |
> | [Microsoft ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | 관리형 서비스 등록 할당을 제거합니다. |
> | [Microsoft ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/Operationstststv/read | 리소스의 작업 상태를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>관리 그룹 참가자

관리 그룹 참가자 역할 [자세히 알아보기](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/Managementg/delete | 관리 그룹을 삭제합니다. |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | 관리 그룹에서 구독의 연결을 해제합니다. |
> | [Microsoft Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | 기존 구독을 관리 그룹과 연결합니다. |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)G/cg/write | 관리 그룹을 만들거나 업데이트합니다. |
> | [Microsoft Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 지정 된 관리 그룹에서 구독을 나열 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>관리 그룹 읽기 권한자

관리 그룹 읽기 권한자 역할

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft. 관리/관리](resource-provider-operations.md#microsoftmanagement)국가/읽기 | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 지정 된 관리 그룹에서 구독을 나열 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>NewRelic APM 계정 참가자

New Relic Application Performance Management 계정 및 애플리케이션을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights 데이터 쓰기 권한자(미리 보기)

리소스 정책에 대한 읽기 액세스 권한과 리소스 구성 요소 정책 이벤트에 대한 쓰기 액세스 권한을 허용합니다. [자세한 정보](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | 정책 할당에 대한 정보를 가져옵니다. |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | 정책 정의에 대한 정보를 가져옵니다. |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | 정책 집합 정의에 대한 정보를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | 특정 구성 요소의 데이터 정책 준수 상태를 확인합니다. |
> | [Microsoft PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | 리소스 구성 요소 정책 이벤트를 기록합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>리소스 정책 참가자

리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 사용자입니다. [자세한 정보](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | 정책 할당 만들기 및 관리 |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | 정책 정의 만들기 및 관리 |
> | [Microsoft 인증](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | 정책 집합 만들기 및 관리 |
> | [Microsoft PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery 참가자

자격 증명 모음 만들기 및 역할 할당을 제외한 Site Recovery 서비스를 관리할 수 있습니다. [자세한 정보](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | 복제 경고 설정 만들기 또는 업데이트 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | 복제 패브릭 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | 복제 정책 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | 복구 계획 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Recovery Services 자격 증명 모음의 스토리지 구성 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | 자격 증명 모음 복제 작업 상태를 읽습니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery 운영자

장애 조치 (failover) 및 장애 복구를 수행할 수 있지만 다른 Site Recovery 관리 작업을 수행할 수 없습니다. [자세한 정보](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | 패브릭의 일관성을 검사합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | 게이트웨이를 다시 연결합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | 패브릭용 인증서 갱신 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 복구 지점을 적용합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 장애 조치(Failover) 커밋 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 계획된 장애 조치(Failover) |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 복제를 복구합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 보호된 항목을 다시 보호합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 보호 컨테이너를 전환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 테스트 장애 조치(Failover) |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 테스트 장애 조치(Failover) 정리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 장애 조치 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 모바일 서비스를 업데이트합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 공급자를 새로 고칩니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | 장애 조치(Failover) 커밋 복구 계획 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | 계획된 장애 조치(Failover) 복구 계획 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | 복구 계획을 다시 보호합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | 테스트 장애 조치(failover) 복구 계획 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 테스트 장애 조치(failover) 정리 복구 계획 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | 장애 조치(Failover) 복구 계획 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery 구독자

Site Recovery 상태를 볼 수 있지만 다른 관리 작업은 수행할 수 없습니다. [자세한 정보](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | 작업을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>지원 요청 참가자

지원 요청을 만들고 관리할 수 있습니다. [자세한 정보](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>태그 기여자

엔터티의 태그를 관리할 수 있으며, 엔터티 자체에 대한 액세스 권한은 없습니다. [자세한 정보](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [/Subscriptions/resourceGroups/resources/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹에 대한 리소스를 가져옵니다. |
> | [/Subscriptions/resources/read](resource-provider-operations.md#microsoftresources) | 구독 리소스를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [/Tags/*](resource-provider-operations.md#microsoftresources) |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>기타


### <a name="biztalk-contributor"></a>BizTalk 참가자

BizTalk Services를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk 서비스 만들기 및 관리 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>데스크톱 가상화 사용자

사용자가 응용 프로그램 그룹에서 응용 프로그램을 사용할 수 있습니다. [자세한 정보](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft DesktopVirtualization/applicationGroups/useApplications/action | ApplicationGroup 사용 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Scheduler 작업 컬렉션 참가자

Scheduler 작업 컬렉션을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft 권한 부여](resource-provider-operations.md#microsoftauthorization)/*/읽기 | 역할 및 역할 할당 읽기 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [/Deployments/*](resource-provider-operations.md#microsoftresources) | 배포를 만들고 관리합니다. |
> | [/Subscriptions/resourceGroups/read](resource-provider-operations.md#microsoftresources) | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | 스케줄러 작업 컬렉션 만들기 및 관리 |
> | [Microsoft 지원](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>다음 단계

- [리소스 공급자를 서비스에 매칭](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 사용자 지정 역할](custom-roles.md)
- [Azure Security Center의 권한](../security-center/security-center-permissions.md)
