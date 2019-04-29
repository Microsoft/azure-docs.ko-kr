---
title: Azure Storage 계정 개요 | Microsoft Docs
description: Azure Storage 계정의 만들기 및 사용을 위한 옵션을 이해합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 52226d07595120395909dd5f47d5d896f5cdaa75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483660"
---
# <a name="azure-storage-account-overview"></a>Azure Storage 계정 개요

Azure Storage 계정에는 Blob, 파일, 큐, 테이블, 디스크 등, 모든 Azure Storage 데이터 개체가 포함됩니다. Azure Storage 계정의 데이터는 지속성 있고 안전하며 가용성과 확장성이 높으며 전세계 어디서나 HTTP 또는 HTTPS를 통해 액세스할 수 있습니다.

Azure Storage 계정을 만드는 방법은 [스토리지 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

## <a name="types-of-storage-accounts"></a>저장소 계정 유형

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>범용 v2 계정

범용 v2 스토리지 계정은 최신 Azure Storage 기능을 지원하고 모든 범용 v1의 기능 및 Blob Storage 계정을 통합합니다. 범용 v2 계정은 업계 경쟁력 있는 트랜잭션 가격 뿐만 아니라 Azure Storage에 대해서도 가장 낮은 기가바이트당 용량 가격을 제공합니다. 범용 v2 스토리지 계정은 다음 Azure Storage 서비스를 지원합니다.

- Blob(모든 유형: 차단, 추가, 페이지)
- 파일
- 디스크
- 큐
- 테이블

> [!NOTE]
> 대부분의 시나리오에서 범용 v2 저장소 계정을 사용하는 것이 좋습니다. 가동 중지 시간이 없고 데이터를 복사할 필요 없이 범용 v1 또는 Blob Storage 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다.
>
> 범용 v2 계정으로의 업그레이드에 대한 자세한 내용은 [범용 v2 저장소 계정으로 업그레이드](storage-account-upgrade.md)를 참조하세요.

범용 v2 저장소 계정은 사용 패턴을 기준으로 데이터 저장을 위한 여러 액세스 계층을 제공합니다. 자세한 내용은 [블록 Blob 데이터에 대한 액세스 계층](#access-tiers-for-block-blob-data)을 참조하세요.

### <a name="general-purpose-v1-accounts"></a>범용 v1 계정

범용 v1 계정은 모든 Azure Storage 서비스에 대한 액세스를 제공하지만 최신 기능 또는 기가바이트당 가장 낮은 가격 책정이 포함되지 않을 수 있습니다. 범용 v1 스토리지 계정은 다음 Azure Storage 서비스를 지원합니다.

- Blob(모든 유형)
- 파일
- 디스크
- 큐
- 테이블

대부분의 경우 범용 v2 계정이 권장되나 다음 시나리오에서는 범용 v1 계정이 가장 적합합니다.

* 애플리케이션에 Azure 클래식 배포 모델이 필요합니다. 범용 v2 계정 및 Blob Storage 계정은 Azure Resource Manager 배포 모델만 지원합니다. 

* 애플리케이션이 트랜잭션이 많거나 상당한 지역 복제 대역폭을 사용하지만 대용량이 필요하지는 않습니다. 이 경우 범용 v1이 가장 경제적인 선택이 될 수 있습니다.

* 2014-02-14 이전 버전인 [Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 나, 4.x 미만인 클라이언트 라이브러리를 사용하며 애플리케이션을 업그레이드할 수 없습니다.

### <a name="block-blob-storage-accounts"></a>블록 blob storage 계정

블록 blob storage 계정은 블록 blob으로 구조화 되지 않은 개체 데이터를 저장 하기 위한 특수 storage 계정 되었거나 추가 blob입니다. 블록 blob storage 계정 사용 패턴을 기반으로 데이터를 저장 하는 것에 대 한 여러 액세스 계층을 제공 합니다. 자세한 내용은 [블록 Blob 데이터에 대한 액세스 계층](#access-tiers-for-block-blob-data)을 참조하세요.

### <a name="filestorage-preview-storage-accounts"></a>저장소 계정 FileStorage (미리 보기)

FileStorage 저장소 계정에는 premium 파일 공유를 만들고 저장 하는 데 특수화 된 저장소 계정이입니다. FileStorage 저장소 계정은 IOPS 버스팅 하는 등의 전용 고유한 성능 특성을 제공합니다. 이러한 특성에 대 한 자세한 내용은 참조는 [파일 공유 성능 계층](../files/storage-files-planning.md#file-share-performance-tiers) 계획 가이드 파일의 섹션입니다.

## <a name="naming-storage-accounts"></a>저장소 계정 이름 지정

저장소 계정의 이름을 지정할 때는 다음 규칙에 유의하세요.

- Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
- 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 두 개의 저장소 계정이 같은 이름을 사용할 수 없습니다.

## <a name="general-purpose-performance-tiers"></a>범용 성능 계층

다음 성능 계층 중 하나에 대해 범용 저장소 계정을 구성할 수 있습니다.

* Blob, 파일, 테이블, 큐 및 Azure 가상 머신 디스크를 저장하기 위한 표준 성능 계층
* 비관리형 가상 머신 디스크만 저장하기 위한 프리미엄 성능 계층

## <a name="access-tiers-for-block-blob-data"></a>블록 Blob 데이터를 위한 액세스 계층

Azure Storage는 사용 패턴에 따라 블록 Blob 데이터 액세스를 위한 여러 가지 옵션을 제공합니다. Azure Storage의 액세스 계층은 특정 데이터 사용 패턴에 맞게 최적화됩니다. 필요에 따라 적합한 계층을 선택하면 가장 경제적인 방식으로 블록 Blob 데이터를 저장할 수 있습니다.

사용 가능한 액세스 계층은 다음과 같습니다.

* **핫** 액세스 계층은 저장소 계층의 개체에 자주 액세스하는 데 최적화되어 있습니다. 핫 계층의 데이터에 액세스 하는 가장 비용 효율적인 저장소 비용이 높습니다. 새 저장소 계정은 기본적으로 핫 계층에 만들어집니다.
* **쿨** 액세스 계층은 자주 액세스하지 않고 최소 30일 동안 저장된 많은 양의 데이터를 저장하는 데 최적화되어 있습니다. 쿨 계층에서 데이터를 저장 하는 것이 더 비용 효율적인 하지만 해당 데이터에 액세스 하는 핫 계층의 데이터에 액세스 하는 보다 비용이 높을 수 있습니다.
* **보관** 계층은 개별 블록 Blob에만 사용할 수 있습니다. 보관 계층은 몇 시간의 검색 대기 시간을 허용할 수 있고, 최소 180일 동안 보관 계층에 남아 있는 데이터에 맞게 최적화되어 있습니다. 보관 계층에 데이터를 저장하는 것이 가장 비용 효율적이지만, 데이터 액세스 비용이 핫 또는 쿨 계층의 데이터에 액세스하는 것보다 높을 수 있습니다.

데이터의 사용 패턴에 변화가 있으면 언제든 이 액세스 계층 간을 전환할 수 있습니다. 액세스 계층에 대 한 자세한 내용은 참조 하세요. [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md)합니다.

> [!IMPORTANT]
> 기존 저장소 계정 또는 Blob에 대한 액세스 계층을 변경하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [저장소 계정 청구 섹션](#storage-account-billing)을 참조하세요.

## <a name="replication"></a>복제

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

스토리지 복제에 대한 자세한 내용은 [Azure Storage 복제](storage-redundancy.md)를 참조하세요.

## <a name="encryption"></a>암호화

저장소 계정의 모든 데이터는 서비스 쪽에서 암호화됩니다. 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](storage-service-encryption.md)를 참조하세요.

## <a name="storage-account-endpoints"></a>Storage 계정 엔드포인트

저장소 계정은 데이터에 대해 Azure에서 고유의 네임스페이스를 제공합니다. Azure Storage 계정에 저장한 모든 개체는 고유 계정 이름을 포함하는 주소를 갖습니다. 계정 이름과 Azure Storage 서비스 엔드포인트의 조합이 스토리지 계정의 엔드포인트가 됩니다.

예를 들어 범용 스토리지 계정의 이름이 *mystorageaccount*일 경우 이 스토리지 계정의 기본 엔드포인트는 다음과 같습니다.

* Blob 스토리지: http://*mystorageaccount*.blob.core.windows.net
* 테이블 스토리지: http://*mystorageaccount*.table.core.windows.net
* 큐 스토리지: http://*mystorageaccount*.queue.core.windows.net
* Azure Files: http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Blob Storage 계정은 Blob 서비스 엔드포인트만 노출합니다.

저장소 계정의 개체에 액세스하기 위한 URL은 저장소 계정의 개체 위치를 엔드포인트에 추가하여 작성됩니다. 예를 들어 Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

Blob에 사용자 지정 도메인 이름을 사용하도록 저장소 계정을 구성할 수도 있습니다. 자세한 내용은 [Azure Storage 계정에 대한 사용자 지정 도메인 이름 구성](../blobs/storage-custom-domain-name.md)을 참조하세요.  

## <a name="control-access-to-account-data"></a>계정 데이터 액세스 제어

기본적으로 계정에 대한 데이터는 사용자 계정 소유자에만 사용할 수 있습니다. 데이터에 액세스할 수 있는 사용자와, 해당 사용자가 보유한 권한을 제어할 수 있습니다.

저장소 계정에 대한 모든 요청은 인증이 필요합니다. 서비스 수준에서 요청에는 유효한 *권한 부여* 헤더가 있어야 합니다. 여기에는 실행에 앞서 서비스가 요청의 유효성을 검사하는 데 필요한 모든 정보가 포함됩니다.

다음 방법 중 하나를 사용하여 저장소 계정에서 데이터에 대한 액세스를 부여할 수 있습니다.

- **Azure Active Directory:** 사용자, 그룹 또는 blob 및 큐 데이터에 대 한 액세스에 대 한 다른 id를 인증 하려면 Azure Active Directory (Azure AD) 자격 증명을 사용 합니다. ID 인증에 성공하면 Azure AD가 Azure Blob Storage나 큐 스토리지에 사용할 토큰을 반환합니다. 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](storage-auth-aad.md)을 참조하세요.
- **공유 키 권한 부여:** 스토리지 계정 액세스 키를 사용하여, Azure Storage에 액세스하기 위해 런타임 시 애플리케이션이 사용하는 연결 문자열을 구성합니다. 연결 문자열의 값을 사용하여 Azure Storage에 전달되는 *권한 부여* 헤더를 구성합니다. 자세한 내용은 [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.
- **공유 액세스 서명:** Azure AD 인증을 사용하지 않는 경우 스토리지 계정의 리소스에 대한 액세스를 위임하기 위해 공유 액세스 서명을 사용합니다. 공유 액세스 서명은 URL의 Azure Storage에 대한 요청을 인증하는 데 필요한 모든 정보를 캡슐화하는 토큰입니다. 저장소 리소스, 부여한 사용 권한, 권한이 유효한 기간을 공유 액세스 서명의 일부로 지정할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

> [!NOTE]
> Azure AD 자격 증명을 통한 사용자 또는 애플리케이션 권한 부여는 다른 인증 수단보다 보안 수준이 높고 사용이 간편합니다. 애플리케이션에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. SAS(공유 액세스 서명)를 계속 사용하여 저장소 계정의 리소스에 세분화된 액세스 권한을 부여할 수도 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다. 
>
> 가능하다면 Azure Storage Blob 및 큐 애플리케이션에 Azure AD 인증을 사용하는 것이 좋습니다.

## <a name="copying-data-into-a-storage-account"></a>저장소 계정에 데이터 복사

Microsoft는 온-프레미스 저장소 디바이스나 타사 클라우드 저장소 공급자에서 데이터를 가져오기 위한 유틸리티와 라이브러리를 제공합니다. 사용하는 솔루션은 전송 데이터의 크기에 따라 다릅니다. 

범용 v1 또는 Blob Storage 계정에서 범용 v2 계정으로 업그레이드할 경우 데이터가 자동으로 마이그레이션됩니다. 이 경로로 계정을 업그레이드하는 것이 좋습니다. 그러나 범용 v1 계정에서 Blob Storage 계정으로 데이터를 이동할 경우 아래에서 설명하는 도구 및 라이브러리를 사용하여 수동으로 데이터를 마이그레이션해야 합니다. 

### <a name="azcopy"></a>AzCopy

AzCopy는 Azure Storage의 데이터를 고속으로 복사하기 위해 설계된 Windows 명령줄 유틸리티입니다. AzCopy를 사용하여 기존 범용 스토리지 계정의 데이터를 Blob Storage 계정으로 복사하거나, 온-프레미스 스토리지 장치의 데이터를 업로드할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

### <a name="data-movement-library"></a>데이터 이동 라이브러리

.NET용 Azure Storage 데이터 이동 라이브러리는 AzCopy를 구동하는 핵심 데이터 이동 프레임워크를 기반으로 합니다. 이 라이브러리는 AzCopy와 유사하게 성능이 높고 안정적이며 사용이 간편한 데이터 전송 작업을 제공합니다. 이 라이브러리를 사용하여 AzCopy의 외부 인스턴스를 실행 및 모니터링하지 않고도 애플리케이션에서 기본적으로 AzCopy가 제공하는 기능을 활용할 수 있습니다. 자세한 내용은 [.NET용 Azure Storage 데이터 이동 라이브러리](https://github.com/Azure/azure-storage-net-data-movement)를 참조하세요.

### <a name="rest-api-or-client-library"></a>REST API 또는 클라이언트 라이브러리

Azure 클라이언트 라이브러리 또는 Azure 스토리지 서비스 REST API 중 하나를 통해 데이터를 Blob Storage 계정으로 마이그레이션하는 사용자 지정 응용 프로그램을 만들 수 있습니다. Azure Storage는 NET, Java, C++, Node.JS, PHP, Ruby, Python 등, 여러 언어와 플랫폼을 위한 다양한 클라이언트 라이브러리를 제공합니다. 이 클라이언트 라이브러리는 재시도 논리, 로깅, 병렬 업로드와 같은 고급 기능을 제공합니다. HTTP/HTTPS 요청이 가능한 모든 언어로 호출할 수 있는 REST API에 대해 바로 개발할 수도 있습니다.

Azure Storage REST API에 대한 자세한 내용은 [Azure Storage REST API 참조](https://docs.microsoft.com/rest/api/storageservices/)를 참조하세요. 

> [!IMPORTANT]
> 클라이언트 쪽 암호화를 사용하여 암호화된 Blob은 Blob에 암호화 관련 메타데이터를 저장합니다. 클라이언트 쪽 암호화를 사용하여 암호화된 Blob을 복사하는 경우 복사 작업에서 Blob 메타데이터, 특히 암호화 관련 메타데이터를 유지해야 합니다. 암호화 메타데이터 없이 Blob을 복사하면 Blob 콘텐츠를 다시 검색할 수 없습니다. 암호화 관련 메타데이터에 대한 자세한 내용은 [Azure Storage 클라이언트 쪽 암호화](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

### <a name="azure-importexport-service"></a>Azure Import/Export 서비스

많은 양의 데이터를 저장소 계정으로 가져올 경우 Azure Import/Export 서비스를 사용하는 것이 좋습니다. Import/Export 서비스를 사용하면 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure Blob Storage 및 Azure Files로 가져올 수 있습니다. 

Import/Export 서비스를 사용하여 데이터를 Azure Blob Storage에서 디스크 드라이브로 전송하고 온-프레미스 사이트로 발송할 수도 있습니다. 하나 이상의 디스크 드라이브에 있는 데이터를 Azure Blob Storage 또는 Azure Files로 가져올 수 있습니다. 자세한 내용은 [Azure Import/Export 서비스란?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)을 참조하세요.

## <a name="storage-account-billing"></a>Storage 계정 사용 비용

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>다음 단계

* Azure Storage 계정을 만드는 방법은 [스토리지 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.
* 기존 스토리지 계정을 관리 또는 삭제하려면 [Azure Storage 계정 관리](storage-account-manage.md)를 참조하세요.
