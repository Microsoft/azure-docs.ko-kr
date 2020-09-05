---
title: Storage 계정 개요
titleSuffix: Azure Storage
description: Azure Storage의 저장소 계정 개요를 참조 하세요. 계정 이름, 성능 계층, 액세스 계층, 중복성, 암호화, 끝점 등을 검토 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4fe353467a11e9581db76ec495194878414f4dfb
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230690"
---
# <a name="storage-account-overview"></a>Storage 계정 개요

Azure Storage 계정에는 Blob, 파일, 큐, 테이블, 디스크 등, 모든 Azure Storage 데이터 개체가 포함됩니다. 저장소 계정은 HTTP 또는 HTTPS를 통해 전 세계 어디에서 나 액세스할 수 있는 Azure Storage 데이터에 대 한 고유한 네임 스페이스를 제공 합니다. Azure storage 계정의 데이터는 지속적이 고 항상 사용 가능 하며 안전 하며 대규모 확장 가능 합니다.

Azure Storage 계정을 만드는 방법은 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

## <a name="types-of-storage-accounts"></a>스토리지 계정 유형

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>범용 v2 계정

범용 v2 스토리지 계정은 최신 Azure Storage 기능을 지원하고 모든 범용 v1의 기능 및 Blob Storage 계정을 통합합니다. 범용 v2 계정은 업계 경쟁력 있는 트랜잭션 가격 뿐만 아니라 Azure Storage에 대해서도 가장 낮은 기가바이트당 용량 가격을 제공합니다. 범용 v2 스토리지 계정은 다음 Azure Storage 서비스를 지원합니다.

- Blob(모든 유형: 블록, 추가, 페이지)
- Data Lake Gen2
- Files
- 디스크
- 큐
- 테이블

> [!NOTE]
> 대부분의 시나리오에서 범용 v2 스토리지 계정을 사용하는 것이 좋습니다. 가동 중지 시간이 없고 데이터를 복사할 필요 없이 범용 v1 또는 Blob Storage 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다.
>
> 범용 v2 계정으로의 업그레이드에 대한 자세한 내용은 [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)를 참조하세요.

범용 v2 스토리지 계정은 사용 패턴을 기준으로 데이터 저장을 위한 여러 액세스 계층을 제공합니다. 자세한 내용은 [블록 Blob 데이터에 대한 액세스 계층](#access-tiers-for-block-blob-data)을 참조하세요.

### <a name="general-purpose-v1-accounts"></a>범용 v1 계정

범용 v1 저장소 계정은 모든 Azure Storage 서비스에 대 한 액세스를 제공 하지만 최신 기능 또는 gb 당 최소 가격 책정을 포함 하지 않을 수 있습니다. 범용 v1 스토리지 계정은 다음 Azure Storage 서비스를 지원합니다.

- Blob(모든 유형)
- Files
- 디스크
- 큐
- 테이블

대부분의 경우 범용 v2 계정을 사용 해야 합니다. 이러한 시나리오에는 범용 v1 계정을 사용할 수 있습니다.

- 애플리케이션에 Azure 클래식 배포 모델이 필요합니다. 범용 v2 계정 및 Blob Storage 계정은 Azure Resource Manager 배포 모델만 지원합니다.

- 응용 프로그램은 트랜잭션 집약적 이거나 상당한 지역 복제 대역폭을 사용 하지만 용량이 많이 필요 하지 않습니다. 이 경우 범용 v1이 가장 경제적인 선택이 될 수 있습니다.

- 2014-02-14 보다 이전 버전의 [저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 또는 보다 낮은 버전의 클라이언트 라이브러리를 사용 합니다. 응용 프로그램을 업그레이드할 수 없습니다.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage 계정

BlockBlobStorage 계정은 구조화 되지 않은 개체 데이터를 블록 blob 또는 추가 blob으로 저장 하기 위한 프리미엄 성능 계층의 특수 저장소 계정입니다. 일반 용도의 v2 및 BlobStorage 계정에 비해 BlockBlobStorage 계정은 낮은, 일관 된 대기 시간 및 더 높은 트랜잭션 속도를 제공 합니다.

BlockBlobStorage 계정은 현재 핫, 쿨 또는 보관 액세스 계층에 대 한 계층화를 지원 하지 않습니다. 이 유형의 저장소 계정은 페이지 blob, 테이블 또는 큐를 지원 하지 않습니다.

### <a name="filestorage-accounts"></a>FileStorage 계정

FileStorage 계정은 프리미엄 파일 공유를 저장 하 고 만드는 데 사용 되는 특수 저장소 계정입니다. 이 저장소 계정 유형은 파일을 지원 하지만 블록 blob, 추가 blob, 페이지 blob, 테이블 또는 큐를 지원 하지 않습니다.

FileStorage 계정은 IOPS 버스트와 같은 고유한 성능 전용 특성을 제공 합니다. 이러한 특성에 대 한 자세한 내용은 파일 계획 가이드의 [파일 공유 저장소 계층](../files/storage-files-planning.md#storage-tiers) 섹션을 참조 하십시오.

## <a name="naming-storage-accounts"></a>스토리지 계정 이름 지정

스토리지 계정의 이름을 지정할 때는 다음 규칙에 유의하세요.

- 스토리지 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
- 스토리지 계정 이름은 Azure 내에서 고유해야 합니다. 두 개의 스토리지 계정이 같은 이름을 사용할 수 없습니다.

## <a name="performance-tiers"></a>성능 계층

만든 저장소 계정 유형에 따라 표준 및 프리미엄 성능 계층 중에서 선택할 수 있습니다.

### <a name="general-purpose-storage-accounts"></a>범용 스토리지 계정

다음 성능 계층 중 하나에 대해 범용 스토리지 계정을 구성할 수 있습니다.

- Blob, 파일, 테이블, 큐 및 Azure 가상 머신 디스크를 저장하기 위한 표준 성능 계층 Standard storage 계정의 확장성 목표에 대 한 자세한 내용은 [standard storage 계정에 대 한 확장성 목표](scalability-targets-standard-account.md)를 참조 하세요.
- 관리 되지 않는 가상 머신 디스크를 저장 하기 위한 프리미엄 성능 계층입니다. 관리 디스크를 관리 되지 않는 디스크 대신 Azure virtual machines와 함께 사용 하는 것이 좋습니다. 프리미엄 성능 계층의 확장성 목표에 대 한 자세한 내용은 [프리미엄 페이지 blob storage 계정에 대 한 확장성 목표](../blobs/scalability-targets-premium-page-blobs.md)를 참조 하세요.

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage 저장소 계정

BlockBlobStorage 저장소 계정은 블록 blob 및 추가 blob을 저장 하기 위한 프리미엄 성능 계층을 제공 합니다. 자세한 내용은 [프리미엄 블록 blob 저장소 계정에 대 한 확장성 목표](../blobs/scalability-targets-premium-block-blobs.md)를 참조 하세요.

### <a name="filestorage-storage-accounts"></a>FileStorage 저장소 계정

FileStorage 저장소 계정은 Azure 파일 공유에 대 한 프리미엄 성능 계층을 제공 합니다. 자세한 내용은 [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)를 참조하세요.

## <a name="access-tiers-for-block-blob-data"></a>블록 Blob 데이터를 위한 액세스 계층

Azure Storage는 사용 패턴에 따라 블록 Blob 데이터 액세스를 위한 여러 가지 옵션을 제공합니다. Azure Storage의 액세스 계층은 특정 데이터 사용 패턴에 맞게 최적화됩니다. 필요에 따라 적합한 계층을 선택하면 가장 경제적인 방식으로 블록 Blob 데이터를 저장할 수 있습니다.

사용 가능한 액세스 계층은 다음과 같습니다.

- **핫** 액세스 계층입니다. 이 계층은 저장소 계정의 개체에 대 한 자주 액세스 하도록 최적화 되어 있습니다. 핫 계층의 데이터에 액세스 하는 것은 가장 비용 효율적 이지만, 저장소 비용은 더 높습니다. 기본적으로 새 스토리지 계정은 핫 계층에 만들어집니다.
- **쿨** 액세스 계층입니다. 이 계층은 드물게 액세스 되 고 최소 30 일 동안 저장 되는 대량의 데이터를 저장 하는 데 최적화 되어 있습니다. 쿨 계층에 데이터를 저장 하는 것이 더 비용 효율적 이지만 데이터에 액세스 하는 것이 핫 계층의 데이터에 액세스 하는 것 보다 비용이 더 많이 들 수 있습니다.
- **보관** 계층입니다. 이 계층은 개별 블록 blob에 대해서만 사용할 수 있습니다. 보관 계층은 몇 시간의 검색 대기 시간을 허용할 수 있고 최소 180 일 동안 보관 계층에 남아 있는 데이터에 대해 최적화 됩니다. 보관 계층은 데이터를 저장 하는 가장 비용 효율적인 옵션입니다. 그러나 해당 데이터에 액세스 하는 것은 핫 또는 쿨 계층의 데이터에 액세스 하는 것 보다 비용이 많이 듭니다.

데이터의 사용 패턴이 변경 되 면 언제 든 지 이러한 액세스 계층 간을 전환할 수 있습니다. 액세스 계층에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md)을 참조 하세요.

> [!IMPORTANT]
> 기존 스토리지 계정 또는 Blob에 대한 액세스 계층을 변경하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [스토리지 계정 청구 섹션](#storage-account-billing)을 참조하세요.

## <a name="redundancy"></a>중복

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>암호화

스토리지 계정의 모든 데이터는 서비스 쪽에서 암호화됩니다. 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](storage-service-encryption.md)를 참조하세요.

## <a name="storage-account-endpoints"></a>Storage 계정 엔드포인트

스토리지 계정은 데이터에 대해 Azure에서 고유의 네임스페이스를 제공합니다. Azure Storage 계정에 저장한 모든 개체는 고유 계정 이름을 포함하는 주소를 갖습니다. 계정 이름과 Azure Storage 서비스 엔드포인트의 조합이 스토리지 계정의 엔드포인트가 됩니다.

예를 들어 범용 스토리지 계정의 이름이 *mystorageaccount*일 경우 이 스토리지 계정의 기본 엔드포인트는 다음과 같습니다.

- Blob 저장소: `https://*mystorageaccount*.blob.core.windows.net`
- 테이블 저장소: `https://*mystorageaccount*.table.core.windows.net`
- 큐 저장소: `https://*mystorageaccount*.queue.core.windows.net`
- Azure Files: `https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> 블록 blob 및 blob 저장소 계정은 Blob service 끝점만 노출 합니다.

저장소 계정의 개체 위치를 끝점에 추가 하 여 저장소 계정의 개체에 액세스 하기 위한 URL을 생성 합니다. 예를 들어 Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

Blob에 사용자 지정 도메인 이름을 사용하도록 스토리지 계정을 구성할 수도 있습니다. 자세한 내용은 [Azure Storage 계정에 대한 사용자 지정 도메인 이름 구성](../blobs/storage-custom-domain-name.md)을 참조하세요.  

## <a name="control-access-to-account-data"></a>계정 데이터 액세스 제어

기본적으로 계정에 대한 데이터는 사용자 계정 소유자에만 사용할 수 있습니다. 데이터에 액세스할 수 있는 사용자와, 해당 사용자가 보유한 권한을 제어할 수 있습니다.

스토리지 계정에 대한 모든 요청은 인증이 필요합니다. 서비스 수준에서 요청은 올바른 *권한 부여* 헤더를 포함 해야 합니다. 특히이 헤더는 서비스에서 요청을 실행 하기 전에 유효성을 검사 하는 데 필요한 모든 정보를 포함 합니다.

다음 방법 중 하나를 사용하여 스토리지 계정에서 데이터에 대한 액세스를 부여할 수 있습니다.

- **Azure Active Directory:** Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 blob 및 큐 데이터에 액세스 하기 위해 사용자, 그룹 또는 기타 id를 인증 합니다. ID 인증에 성공하면 Azure AD가 Azure Blob Storage나 큐 스토리지에 사용할 토큰을 반환합니다. 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](storage-auth-aad.md)을 참조하세요.
- **공유 키 권한 부여:** 스토리지 계정 액세스 키를 사용하여, Azure Storage에 액세스하기 위해 런타임에 애플리케이션이 사용하는 연결 문자열을 구성합니다. 연결 문자열의 값을 사용하여 Azure Storage에 전달되는 *권한 부여* 헤더를 구성합니다. 자세한 내용은 [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.
- **공유 액세스 서명:** Azure AD 권한 부여를 사용 하지 않는 경우 공유 액세스 서명을 사용 하 여 저장소 계정의 리소스에 대 한 액세스를 위임 합니다. 공유 액세스 서명은 URL의 Azure Storage에 대한 요청을 인증하는 데 필요한 모든 정보를 캡슐화하는 토큰입니다. 스토리지 리소스, 부여한 사용 권한, 권한이 유효한 기간을 공유 액세스 서명의 일부로 지정할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)을 참조하세요.

> [!NOTE]
> Azure AD 자격 증명을 통한 사용자 또는 애플리케이션 권한 부여는 다른 인증 수단보다 보안 수준이 높고 사용이 간편합니다. 애플리케이션에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. SAS(공유 액세스 서명)를 계속 사용하여 스토리지 계정의 리소스에 세분화된 액세스 권한을 부여할 수도 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다.
>
> 가능 하면 Azure Storage blob 및 큐 응용 프로그램에 Azure AD 인증을 사용 하는 것이 좋습니다.

## <a name="copying-data-into-a-storage-account"></a>스토리지 계정에 데이터 복사

Microsoft는 온-프레미스 스토리지 디바이스나 타사 클라우드 스토리지 공급자에서 데이터를 가져오기 위한 유틸리티와 라이브러리를 제공합니다. 사용할 솔루션은 전송 중인 데이터의 양에 따라 달라 집니다.

범용 v1 또는 Blob Storage 계정에서 범용 v2 계정으로 업그레이드할 경우 데이터가 자동으로 마이그레이션됩니다. 이 경로로 계정을 업그레이드하는 것이 좋습니다. 그러나 범용 v1 계정에서 Blob storage 계정으로 데이터를 이동 하려는 경우 아래에 설명 된 도구 및 라이브러리를 사용 하 여 데이터를 수동으로 마이그레이션합니다.

### <a name="azcopy"></a>AzCopy

AzCopy는 Azure Storage의 데이터를 고속으로 복사하기 위해 설계된 Windows 명령줄 유틸리티입니다. AzCopy를 사용하여 기존 범용 스토리지 계정의 데이터를 Blob Storage 계정으로 복사하거나, 온-프레미스 스토리지 디바이스의 데이터를 업로드할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티를 사용 하 여 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조 하세요.

### <a name="data-movement-library"></a>데이터 이동 라이브러리

.NET용 Azure Storage 데이터 이동 라이브러리는 AzCopy를 구동하는 핵심 데이터 이동 프레임워크를 기반으로 합니다. 이 라이브러리는 AzCopy와 유사하게 성능이 높고 안정적이며 사용이 간편한 데이터 전송 작업을 제공합니다. 데이터 이동 라이브러리를 사용 하 여 기본적으로 AzCopy 기능을 활용할 수 있습니다. 자세한 내용은 [.net 용 데이터 이동 라이브러리 Azure Storage](https://github.com/Azure/azure-storage-net-data-movement) 를 참조 하세요.

### <a name="rest-api-or-client-library"></a>REST API 또는 클라이언트 라이브러리

범용 v1 저장소 계정에서 Blob storage 계정으로 데이터를 마이그레이션하는 사용자 지정 응용 프로그램을 만들 수 있습니다. Azure 클라이언트 라이브러리 또는 Azure Storage 서비스 REST API 중 하나를 사용 합니다. Azure Storage는 NET, Java, C++, Node.JS, PHP, Ruby, Python 등, 여러 언어와 플랫폼을 위한 다양한 클라이언트 라이브러리를 제공합니다. 이 클라이언트 라이브러리는 재시도 논리, 로깅, 병렬 업로드와 같은 고급 기능을 제공합니다. HTTP/HTTPS 요청이 가능한 모든 언어로 호출할 수 있는 REST API에 대해 바로 개발할 수도 있습니다.

Azure Storage REST API에 대한 자세한 내용은 [Azure Storage REST API 참조](https://docs.microsoft.com/rest/api/storageservices/)를 참조하세요.

> [!IMPORTANT]
> 클라이언트 쪽 암호화를 사용하여 암호화된 Blob은 Blob에 암호화 관련 메타데이터를 저장합니다. 클라이언트 쪽 암호화를 사용하여 암호화된 Blob을 복사하는 경우 복사 작업에서 Blob 메타데이터, 특히 암호화 관련 메타데이터를 유지해야 합니다. 암호화 메타데이터 없이 Blob을 복사하면 Blob 콘텐츠를 다시 검색할 수 없습니다. 암호화 관련 메타데이터에 대한 자세한 내용은 [Azure Storage 클라이언트 쪽 암호화](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

## <a name="storage-account-billing"></a>Storage 계정 사용 비용

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>다음 단계

- [스토리지 계정을 만드는](storage-account-create.md)
- [블록 Blob 스토리지 계정 만들기](../blobs/storage-blob-create-account-block-blob.md)
