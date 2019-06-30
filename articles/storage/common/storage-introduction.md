---
title: Azure Storage 소개 - Azure의 클라우드 스토리지 | Microsoft Docs
description: Azure Storage는 Microsoft의 클라우드 스토리지 솔루션입니다. Azure Storage는 고가용성, 보안, 지속형, 확장성이 뛰어난 중복된 데이터 개체에 대한 스토리지를 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 51255bc4a93fbe3719eec96f00940a765644bbfe
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312866"
---
# <a name="introduction-to-azure-storage"></a>Azure Storage 소개

Azure Storage는 최신 데이터 스토리지 시나리오를 위한 Microsoft의 클라우드 스토리지 솔루션입니다. Azure Storage는 데이터 개체에 대한 확장성 있는 개체 저장소, 클라우드에 대한 파일 시스템 서비스, 신뢰할 수 있는 메시징을 위한 메시징 저장소 및 NoSQL 저장소를 제공합니다. Azure Storage는:

- **내구성 및 고가용성** 중복성을 통해 일시적인 하드웨어 오류가 발생할 경우 데이터를 안전하게 보호합니다. 또한 로컬 재해 또는 자연 재해로부터 추가 보호를 위해 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다.
- **보안** Azure Storage에 기록된 모든 데이터는 서비스에 의해 암호화됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자에 대한 세분화된 제어를 제공합니다.
- **확장성** Azure Storage는 오늘날의 응용 프로그램의 데이터 스토리지 및 성능 요구 사항을 충족하기 위해 확장성이 매우 뛰어나도록 설계되었습니다. 
- **관리** Microsoft Azure에서 하드웨어 유지 관리, 업데이트 및 중요한 문제를 처리합니다.
- **액세스 가능** Azure Storage의 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. Microsoft는 다양 한 언어로,.NET, Java, Node.js, Python, PHP, Ruby, Go 및 다른 뿐만 완성도 높은 REST API를 포함 하 여 Azure storage 클라이언트 라이브러리를 제공 합니다. Azure Storage는 Azure PowerShell 또는 Azure CLI에서 스크립트를 지원합니다. 또한 Azure Portal 및 Azure Storage Explorer는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공합니다.  

## <a name="azure-storage-services"></a>Azure Storage 서비스

Azure Storage는 이러한 데이터 서비스를 포함합니다.

- [Azure Blob](../blobs/storage-blobs-introduction.md): 텍스트 및 이진 데이터에 대한 확장성이 뛰어난 개체 저장소입니다.
- [Azure 파일](../files/storage-files-introduction.md): 클라우드 또는 온-프레미스 배포에 대한 관리형 파일 공유입니다.
- [Azure 큐](../queues/storage-queues-introduction.md): 애플리케이션 구성 요소 간에 안정적인 메시지를 위한 메시지 저장소입니다. 
- [Azure 테이블](../tables/table-storage-overview.md): 정형 데이터의 스키마 없는 스토리지를 위한 NoSQL 저장소입니다.

각 서비스는 저장소 계정을 통해 액세스됩니다. 시작하려면 [저장소 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

## <a name="blob-storage"></a>Blob 저장소

Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. Blob Storage는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 데이터를 저장하는 데 최적화되어 있습니다. 

Blob Storage는 다음에 이상적입니다.

- 브라우저에 이미지 또는 문서 직접 제공
- 분산 액세스용 파일 저장
- 동영상 및 오디오 스트리밍
- 백업/복원, 재해 복구 및 보관용 데이터 저장
- 온-프레미스 또는 Azure 호스티드 서비스에 의한 분석용 데이터 저장

Blob Storage의 개체는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. 사용자 또는 클라이언트 애플리케이션은 URL, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 Blob에 액세스할 수 있습니다. 저장소 클라이언트 라이브러리는 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) 및 [Ruby](https://azure.github.io/azure-storage-ruby)를 비롯한 여러 언어에서 사용할 수 있습니다.

Blob 스토리지에 대한 자세한 내용은 [Blob 스토리지 소개](../blobs/storage-blobs-introduction.md)를 참조하세요.

## <a name="azure-files"></a>Azure 파일

[Azure Files](../files/storage-files-introduction.md)를 사용하면 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 액세스할 수 있는 고가용성 네트워크 파일 공유를 설정할 수 있습니다. 즉, 여러 VM이 읽기 및 쓰기 권한을 모두 사용하여 동일한 파일을 공유할 수 있습니다. 또한 REST 인터페이스 또는 저장소 클라이언트 라이브러리를 사용하여 파일을 읽을 수 있습니다.

Azure Files가 회사 파일 공유의 파일과 다른 점 한 가지는 파일을 가리키고 SAS(공유 액세스 서명) 토큰을 포함하고 있는 URL을 사용하여 전 세계 어디서나 파일에 액세스할 수 있다는 것입니다. SAS 토큰은 생성 가능하며 특정 기간에 프라이빗 자산에 대한 특정 액세스를 허용합니다.

파일 공유는 다음과 같은 여러 가지 일반적인 시나리오에 사용할 수 있습니다.

- 여러 온-프레미스 애플리케이션에서 파일 공유를 사용합니다. 이 기능을 사용하면 데이터를 공유하는 애플리케이션을 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 파일 공유를 온-프레미스 애플리케이션에서 사용하는 것과 동일한 드라이브 문자에 탑재하면 애플리케이션에서 파일 공유에 액세스하는 부분을 변경하지 않거나 최소한의 변경만 하고도 작동할 것입니다.

- 구성 파일을 파일 공유에 저장하고 여러 VM에서 액세스할 수 있습니다. 그룹의 여러 개발자가 사용하는 도구 및 유틸리티를 파일 공유에 저장할 수 있으며, 이렇게 하면 모든 사람이 찾아서 동일한 버전을 사용할 수 있습니다.

- 파일 공유에 쓰고 나중에 처리하거나 분석할 수 있는 데이터의 세 가지 예로 진단 로그, 메트릭 및 크래시 덤프를 들 수 있습니다.

현재는 Active Directory 기반 인증과 ACL(액세스 제어 목록)이 지원되지 않지만 향후 지원할 예정입니다. 파일 공유 액세스에 대한 인증을 제공하기 위해 저장소 계정 자격 증명이 사용됩니다. 즉, 공유가 탑재된 모든 사용자는 공유에 대한 전체 읽기/쓰기 액세스 권한을 갖습니다.

Azure Files에 대한 자세한 내용은 [Azure Files 소개](../files/storage-files-introduction.md)를 참조하세요.

## <a name="queue-storage"></a>큐 저장소

Azure 큐 서비스는 메시지를 저장하고 검색하는 데 사용됩니다. 큐 메시지의 크기는 최대 64KB일 수 있고 큐에는 수 많은 메시지가 포함될 수 있습니다. 큐는 일반적으로 비동기적으로 처리될 메시지의 목록을 저장하는 데 사용됩니다.

예를 들어 고객이 사진을 업로드하여 각 사진에 대한 썸네일을 만들려고 한다고 가정하겠습니다. 고객이 사진을 업로드하는 동안 썸네일을 만들 때까지 기다리게 할 수 있습니다. 대신 큐를 사용할 수 있습니다. 고객의 업로드가 완료 되 면 큐에 메시지를 기록 합니다. 그런 다음 Azure Function에서는 큐의 메시지를 검색하고 썸네일을 만듭니다. 이 프로세스의 일부는 각각 별도로 확장될 수 있으며 사용하기 위해 조정하는 경우 더 세밀하게 조정할 수 있습니다.

Azure 큐에 대한 자세한 내용은 [큐 소개](../queues/storage-queues-introduction.md)를 참조하세요.

## <a name="table-storage"></a>테이블 저장소

Azure Table Storage는 이제 Azure Cosmos DB의 일부입니다. Azure Table Storage 설명서를 보려면 [Azure Table Storage 개요](../tables/table-storage-overview.md)를 참조하세요. 기존 Azure Table Storage 서비스 외에도 처리량 최적화 테이블, 전역 분산 및 자동 보조 인덱스를 제공하는 새로운 Azure Cosmos DB 테이블 API가 있습니다. 새로운 프리미엄 환경에 대해 알아보고 사용해 보려면 [Azure Cosmos DB 테이블 API](https://aka.ms/premiumtables)를 확인하세요.

Table Storage에 대한 자세한 내용은 [Azure Table Storage 개요](../tables/table-storage-overview.md)를 참조하세요.

## <a name="disk-storage"></a>디스크 저장소

Azure Managed Disk는 VHD(가상 하드 디스크)입니다. 온-프레미스 서버의 물리적 디스크처럼 생각할 수 있지만 가상화된 디스크입니다. Azure Managed Disks는 Azure의 무작위 IO 스토리지 개체인 페이지 Blob으로 저장됩니다. 관리 디스크를 ‘관리’로 부르는 것은 페이지 Blob, Blob 컨테이너 및 Azure Storage 계정에 대한 추상이기 때문입니다. 관리 디스크를 사용할 경우 디스크를 프로비저닝하기만 하면 Azure에서 나머지 작업을 처리합니다.

관리 디스크(Managed disk)에 대한 자세한 내용은 [Azure 관리 디스크 개요](../../virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

## <a name="types-of-storage-accounts"></a>저장소 계정 유형

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

## <a name="securing-access-to-storage-accounts"></a>저장소 계정에 대 한 액세스 보호

Azure Storage에 대 한 모든 요청을 인증 해야 합니다. Azure Storage에는 다음 인증 방법을 지원합니다.

- **Blob 및 큐 데이터에 대 한 azure Active Directory (Azure AD) 통합.** Azure Storage Blob 및 큐 서비스의 역할 기반 액세스 제어 (RBAC)를 통해 인증 및 Azure AD 사용 하 여 권한 부여를 지원합니다. 뛰어난 보안 및 사용 편의성에 대 한 Azure AD 사용 하 여 요청 권한 부여 것이 좋습니다. 자세한 내용은 [Azure에 대 한 권한 부여 액세스 blob 및 Azure Active Directory를 사용 하 여 큐](storage-auth-aad.md)합니다.
- **Azure AD 권한 부여 SMB 통한 Azure Files (미리 보기)에 대 한 합니다.** Azure Files는 Azure Active Directory Domain Services를 통해 SMB (서버 메시지 블록)를 통한 id 기반 권한 부여를 지원 합니다. 도메인에 가입 된 Windows 가상 컴퓨터 (Vm)는 Azure AD 자격 증명을 사용 하 여 Azure 파일 공유를 액세스할 수 있습니다. 자세한 내용은 [SMB 통한 Azure Files (미리 보기)에 대 한 개요의 Azure Active Directory 인증](../files/storage-files-active-directory-overview.md)합니다.
- **공유 키를 사용 하 여 권한 부여 합니다.** Azure Storage Blob, Queue 및 Table services 및 Azure Files 공유 Key.A 클라이언트 권한 부여 저장소 계정 액세스 키를 사용 하 여 서명 된 모든 요청과 함께 헤더를 전달 하는 공유 키를 사용 하 여 권한 부여를 지원 합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.
- **권한 부여를 사용 하 여 공유 액세스 서명 (SAS).** 공유 액세스 서명 (SAS)은 저장소 리소스에 대 한 URI에 추가할 수 있는 보안 토큰을 포함 하는 문자열입니다. 보안 토큰 사용 권한과 액세스 기간 같은 제약 조건이 캡슐화합니다. 자세한 내용은 참조 [공유 액세스 서명 (SAS)를 사용 하 여](storage-dotnet-shared-access-signature-part-1.md)입니다.
- **컨테이너 및 blob에 익명 액세스 합니다.** 컨테이너와 해당 blob 공개적으로 사용할 수 있습니다. 컨테이너 또는 blob을 공용 인지를 지정 하면 모든 사용자가 읽을 수 익명; 인증이 필요 하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.

## <a name="encryption"></a>암호화

Storage 서비스에 사용할 수 있는 기본적인 종류의 두 가지 암호화가 있습니다. 보안 및 암호화에 대한 자세한 내용은 [Azure Storage 보안 가이드](storage-security-guide.md)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

보호 하 고 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하는 azure Storage 암호화. Azure Storage는 자동으로 저장소 계정에 유지 하기 전에 모든 데이터를 암호화 하 고 검색 하기 전에 암호를 해독 합니다. 암호화, 암호 해독 및 키 관리 프로세스는 사용자에 게 완전히 투명 합니다. 고객은 Azure Key Vault를 사용 하 여 고유한 키를 관리할 수도 있습니다. 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](storage-service-encryption.md)합니다.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

Azure Storage 클라이언트 라이브러리는 네트워크를 통해 보내고 응답을 암호 해독 하기 전에 클라이언트 라이브러리에서 데이터를 암호화 하기 위한 메서드를 제공 합니다. 또한 클라이언트 쪽 암호화를 통해 암호화 된 데이터는 Azure Storage에서 미사용 시 암호화 됩니다. 클라이언트 쪽 암호화에 대 한 자세한 내용은 참조 하세요. [Azure Storage 용.NET을 사용 하 여 클라이언트 쪽 암호화](storage-client-side-encryption.md)합니다.

## <a name="redundancy"></a>중복

데이터가 지속되는지 확인하기 위해 Azure Storage는 여러 데이터 복사본을 복제합니다. 스토리지 계정을 설정할 때 중복 옵션을 선택합니다.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

재해 복구에 대한 자세한 내용은 [Azure Storage에서 재해 복구 및 스토리지 계정 장애 조치(failover)(미리 보기)](storage-disaster-recovery-guidance.md)를 참조하세요.

## <a name="transferring-data-to-and-from-azure-storage"></a>Azure Storage 간에 데이터 전송

Azure Storage에서 데이터를 이동하는 몇 가지 옵션이 있습니다. 선택하는 옵션은 데이터 세트의 크기와 네트워크 대역폭에 따라 달라집니다. 자세한 내용은 [데이터 전송을 위한 Azure 솔루션 선택](storage-choose-data-transfer-solution.md)을 참조하세요.

## <a name="pricing"></a>가격

Azure Storage에서 가격 책정에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="storage-apis-libraries-and-tools"></a>Storage API, 라이브러리 및 도구

Azure Storage 리소스는 HTTP/HTTPS 요청을 수행할 수 있는 모든 언어로 액세스할 수 있습니다. 또한 Azure Storage는 많이 사용되는 몇 가지 언어를 위한 프로그래밍 라이브러리를 제공합니다. 이 라이브러리는 동기/비동기 호출, 작업 일괄 처리, 예외 관리, 자동 재시도, 작업자 동작 등과 같은 세부 사항을 처리하여 Azure Storage 작업의 많은 측면을 간소화합니다. 현재 이 라이브러리는 파이프라인의 다른 라이브러리와 함께 다음 언어 및 플랫폼에 대해 사용할 수 있습니다.

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage 데이터 API 및 라이브러리 참조

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [.NET 용 azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Java/Android 용 azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Node.js 용 azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/javascript/api/azure-storage)
- [Python 용 azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-python)
- [PHP 용 azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php)
- [Ruby 용 azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-ruby)
- [에 대 한 azure Storage 클라이언트 라이브러리C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage 관리 API 및 라이브러리 참조

- [Storage 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/storagerp/)
- [.NET용 Storage 리소스 공급자 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [Storage 서비스 관리 REST API(클래식](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage 데이터 이동 API 및 라이브러리 참조

- [저장소 Import/Export 서비스 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
- [.NET용 저장소 데이터 이동 클라이언트 라이브러리](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>도구 및 유틸리티

- [Storage용 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.storage)
- [Storage용 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy 명령줄 유틸리티](https://aka.ms/downloadazcopy)
- [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
- [Azure Storage 클라이언트 도구](../storage-explorers.md)
- [Azure 개발자 도구](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>다음 단계

Azure Storage를 사용하여 가동하고 실행하려면 [스토리지 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.
