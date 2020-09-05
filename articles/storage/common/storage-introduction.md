---
title: Azure Storage 소개 - Azure의 클라우드 스토리지 | Microsoft Docs
description: 핵심 Azure Storage 플랫폼은 Microsoft의 클라우드 저장소 솔루션입니다. Azure Storage는 고가용성, 보안, 지속형, 확장성이 뛰어난 중복된 데이터 개체에 대한 스토리지를 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b603776ce19bca8d6fefa7c3c85366ebe3b7b01f
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653803"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>핵심 Azure Storage 서비스 소개

Azure Storage 플랫폼은 최신 데이터 저장소 시나리오를 위한 Microsoft의 클라우드 저장소 솔루션입니다. 핵심 저장소 서비스는 데이터 개체에 대 한 대규모 확장 가능한 개체 저장소, Azure Vm (가상 머신) 용 디스크 저장소, 클라우드의 파일 시스템 서비스, 신뢰할 수 있는 메시징의 메시징 저장소 및 NoSQL 저장소를 제공 합니다. 서비스는 다음과 같습니다.

- **내구성 및 고가용성** 중복성은 일시적인 하드웨어 오류 발생 시 데이터를 안전하게 보호합니다. 또한 로컬 재해 또는 자연 재해로부터 추가 보호를 위해 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이 방식으로 복제된 데이터는 예기치 않은 중단 시에도 고가용성을 유지합니다.
- **보안.** Azure 저장소 계정에 기록 된 모든 데이터는 서비스에 의해 암호화 됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자를 자세히 제어할 수 있습니다.
- **크기.** Azure Storage는 오늘날의 애플리케이션에 대한 데이터 저장소 및 성능 요구 사항을 충족하기 위해 대규모로 확장할 수 있도록 설계되었습니다.
- **Managed.** Azure는 하드웨어 유지 관리, 업데이트 및 중요 한 문제를 처리 합니다.
- **액세스 가능** Azure Storage의 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. Microsoft는 .NET, Java, Node.js, Python, PHP, Ruby, Go 등을 비롯 한 다양 한 언어로 Azure Storage 용 클라이언트 라이브러리를 제공 하 REST API 고 있습니다. Azure Storage는 Azure PowerShell 또는 Azure CLI에서 스크립트를 지원합니다. 또한 Azure Portal 및 Azure Storage Explorer는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공합니다.  

## <a name="core-storage-services"></a>핵심 저장소 서비스

Azure Storage 플랫폼은 다음과 같은 데이터 서비스를 포함 합니다.

- [Azure Blob](../blobs/storage-blobs-introduction.md): 텍스트 및 이진 데이터에 대한 확장성이 뛰어난 개체 저장소입니다. 또한 Data Lake Storage Gen2를 통한 빅 데이터 분석에 대 한 지원도 포함 합니다.
- [Azure Files](../files/storage-files-introduction.md): 클라우드 또는 온-프레미스 배포를 위한 관리 되는 파일 공유입니다.
- [Azure 큐](../queues/storage-queues-introduction.md): 애플리케이션 구성 요소 간에 안정적인 메시징을 위한 메시징 저장소입니다.
- [Azure 테이블](../tables/table-storage-overview.md): 구조화된 데이터의 스키마 없는 스토리지를 위한 NoSQL 스토리지입니다.
- [Azure 디스크](../../virtual-machines/managed-disks-overview.md): azure vm에 대 한 블록 수준 저장소 볼륨입니다.

각 서비스는 스토리지 계정을 통해 액세스됩니다. 시작하려면 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

## <a name="example-scenarios"></a>예제 시나리오

다음 표에서는 파일, Blob, 디스크, 큐 및 테이블을 비교 하 고 각각에 대 한 예제 시나리오를 보여 줍니다.

| 기능 | Description | 사용 시기 |
|--------------|-------------|-------------|
| **Azure 파일** |는 업계 표준 SMB (서버 메시지 블록) 프로토콜을 통해 어디에서 나 액세스할 수 있는 완전히 관리 되는 클라우드 파일 공유를 제공 합니다.<br><br>Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포에서 Azure 파일 공유를 탑재할 수 있습니다. | 이미 네이티브 파일 시스템 Api를 사용 하는 클라우드로 응용 프로그램을 "리프트 앤 시프트" 하 여 Azure에서 실행 되는 응용 프로그램과 다른 응용 프로그램 간에 데이터를 공유 하려고 합니다.<br/><br/>온-프레미스 파일 서버 또는 NAS 장치를 교체 하거나 보충 하려고 합니다.<br><br> 여러 가상 머신에서 액세스해야 하는 개발 및 디버깅 도구를 저장하려고 합니다. |
| **Azure Blob** | 구조화 되지 않은 데이터를 블록 blob에서 대규모로 저장 하 고 액세스할 수 있습니다.<br/><br/>엔터프라이즈 빅 데이터 분석 솔루션을 위한 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)도 지원합니다. | 애플리케이션에서 스트리밍 및 임의 액세스 시나리오를 지원하도록 하려고 합니다.<br/><br/>어디에서든 애플리케이션 데이터에 액세스할 수 있게 되기를 원합니다.<br/><br/>Azure에서 엔터프라이즈 Data Lake를 빌드하고 빅 데이터 분석을 수행하려고 합니다. |
| **Azure 디스크** | 연결 된 가상 하드 디스크에서 데이터를 영구적으로 저장 하 고 액세스할 수 있습니다. | 네이티브 파일 시스템 Api를 사용 하는 응용 프로그램을 "리프트 앤 시프트" 하 여 데이터를 영구적 디스크로 읽고 쓰는 것이 좋습니다.<br/><br/>가상 머신 외부에서 액세스할 필요가 없는 데이터를 디스크가 연결된 컴퓨터에 저장하려고 합니다. |
| **Azure 큐** | 응용 프로그램 구성 요소 간의 비동기 메시지 큐를 허용 합니다. | 응용 프로그램 구성 요소를 분리 하 고 비동기 메시징을 사용 하 여 서로 통신 하려고 합니다.<br><br>큐 저장소와 Service Bus 큐를 사용 하는 경우에 대 한 지침은 [저장소 큐 및 Service Bus 큐-비교 및 대조](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)를 참조 하세요. |
| **Azure Tables** | 구조화 된 NoSQL 데이터를 클라우드에 저장 하 여 스키마 없는 디자인에 키/특성 저장소를 제공할 수 있습니다. | 웹 응용 프로그램, 주소록, 장치 정보 또는 서비스에 필요한 다른 유형의 메타 데이터에 대 한 사용자 데이터와 같은 유연한 데이터 집합을 저장 하려고 합니다. <br/><br/>테이블 저장소와 Azure Cosmos DB Table API를 사용 하는 경우에 대 한 지침은 [Azure Cosmos DB Table API 및 Azure Table storage](../../cosmos-db/table-support.md)를 사용 하 여 개발을 참조 하세요. |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. Blob Storage는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 데이터를 저장하는 데 최적화되어 있습니다.

Blob 스토리지가 적합한 경우는 다음과 같습니다.

- 브라우저에 이미지 또는 문서 직접 제공
- 분산 액세스용 파일 저장.
- 비디오 및 오디오 스트리밍.
- 백업/복원, 재해 복구 및 보관용 데이터 저장
- 온-프레미스 또는 Azure 호스팅 서비스에서 분석하기 위한 데이터 저장.

Blob Storage의 개체는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. 사용자 또는 클라이언트 애플리케이션은 URL, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 Blob에 액세스할 수 있습니다. 스토리지 클라이언트 라이브러리는 [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) 및 [Ruby](https://azure.github.io/azure-storage-ruby)를 비롯한 여러 언어에서 사용할 수 있습니다.

Blob 스토리지에 대한 자세한 내용은 [Blob 스토리지 소개](../blobs/storage-blobs-introduction.md)를 참조하세요.

## <a name="azure-files"></a>Azure 파일

[Azure Files](../files/storage-files-introduction.md) 를 사용 하면 표준 SMB (서버 메시지 블록) 프로토콜을 사용 하 여 액세스할 수 있는 항상 사용 가능한 네트워크 파일 공유를 설정할 수 있습니다. 즉, 여러 VM이 읽기 및 쓰기 권한을 모두 사용하여 동일한 파일을 공유할 수 있습니다. REST 인터페이스 또는 스토리지 클라이언트 라이브러리를 사용하여 파일을 읽을 수도 있습니다.

Azure Files가 회사 파일 공유의 파일과 다른 점 한 가지는 파일을 가리키고 SAS(공유 액세스 서명) 토큰을 포함하고 있는 URL을 사용하여 전 세계 어디서나 파일에 액세스할 수 있다는 것입니다. SAS 토큰은 생성 가능하며 특정 기간에 프라이빗 자산에 대한 특정 액세스를 허용합니다.

파일 공유를 사용할 수 있는 여러 가지 일반적인 시나리오는 다음과 같습니다.

- 여러 온-프레미스 애플리케이션에서 파일 공유를 사용합니다. 이 기능을 사용하면 데이터를 공유하는 애플리케이션을 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 파일 공유를 온-프레미스 애플리케이션에서 사용하는 것과 동일한 드라이브 문자에 탑재하면 파일 공유에 액세스하는 애플리케이션의 일부가 최소한의 변경 내용(있는 경우)으로 작동해야 합니다.

- 구성 파일을 파일 공유에 저장하고 여러 VM에서 액세스할 수 있습니다. 그룹의 여러 개발자가 사용하는 도구 및 유틸리티를 파일 공유에 저장할 수 있으며, 이렇게 하면 모든 사람이 찾아서 동일한 버전을 사용할 수 있습니다.

- 리소스 로그, 메트릭 및 크래시 덤프는 파일 공유에 기록 하 고 나중에 처리 하거나 분석할 수 있는 데이터의 세 가지 예일 뿐입니다.

Azure Files에 대한 자세한 내용은 [Azure Files 소개](../files/storage-files-introduction.md)를 참조하세요.

일부 SMB 기능은 클라우드에 적용되지 않습니다. 자세한 내용은 [Azure File 서비스에서 지원되지 않는 기능](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)을 참조하세요.

## <a name="queue-storage"></a>Queue Storage

Azure 큐 서비스는 메시지를 저장하고 검색하는 데 사용됩니다. 큐 메시지의 크기는 최대 64KB일 수 있고 큐에는 수 많은 메시지가 포함될 수 있습니다. 큐는 일반적으로 비동기적으로 처리할 메시지의 목록을 저장하는 데 사용됩니다.

예를 들어 고객이 사진을 업로드하여 각 사진에 대한 썸네일을 만들려고 한다고 가정하겠습니다. 고객이 사진을 업로드하는 동안 썸네일을 만들 때까지 기다리게 할 수 있습니다. 대신 큐를 사용할 수 있습니다. 고객이 업로드를 완료 하면 큐에 메시지를 기록 합니다. 그런 다음 Azure Function에서는 큐의 메시지를 검색하고 썸네일을 만듭니다. 이 프로세스의 일부는 각각 별도로 확장될 수 있으며 사용하기 위해 조정하는 경우 더 세밀하게 조정할 수 있습니다.

Azure 큐에 대한 자세한 내용은 [큐 소개](../queues/storage-queues-introduction.md)를 참조하세요.

## <a name="table-storage"></a>Table Storage

Azure Table Storage는 이제 Azure Cosmos DB의 일부입니다. Azure Table Storage 설명서를 보려면 [Azure Table Storage 개요](../tables/table-storage-overview.md)를 참조하세요. 기존 Azure Table Storage 서비스 외에도 처리량 최적화 테이블, 전역 분산 및 자동 보조 인덱스를 제공하는 새로운 Azure Cosmos DB 테이블 API가 있습니다. 새로운 프리미엄 환경에 대해 알아보고 사용해 보려면 [Azure Cosmos DB Table API](https://aka.ms/premiumtables)를 참조 하세요.

Table Storage에 대한 자세한 내용은 [Azure Table Storage 개요](../tables/table-storage-overview.md)를 참조하세요.

## <a name="disk-storage"></a>디스크 스토리지

Azure Managed Disk는 VHD(가상 하드 디스크)입니다. 온-프레미스 서버의 물리적 디스크처럼 생각할 수 있지만 가상화된 디스크입니다. Azure 관리 디스크는 Azure의 임의의 IO 저장소 개체인 페이지 blob으로 저장 됩니다. 관리 디스크 ' 관리 '는 페이지 blob, blob 컨테이너 및 Azure storage 계정에 대 한 추상화 이므로이를 호출 합니다. 관리 디스크를 사용할 경우 디스크를 프로비저닝하기만 하면 Azure에서 나머지 작업을 처리합니다.

관리 디스크에 대 한 자세한 내용은 [Azure managed Disks 소개](../../virtual-machines/managed-disks-overview.md)를 참조 하세요.

## <a name="types-of-storage-accounts"></a>스토리지 계정 유형

Azure Storage는 여러 유형의 저장소 계정을 제공 합니다. 각각의 유형은 서로 다른 기능을 지원하며 고유의 가격 책정 모델이 있습니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

## <a name="secure-access-to-storage-accounts"></a>저장소 계정에 대 한 액세스 보호

Azure Storage에 대 한 모든 요청에는 권한이 있어야 합니다. Azure Storage는 다음과 같은 권한 부여 방법을 지원 합니다.

- **Blob 및 큐 데이터를 위한 Azure Active Directory (Azure AD) 통합** Azure Storage는 RBAC (역할 기반 액세스 제어)를 통해 Blob 및 큐 서비스에 대해 Azure AD를 사용 하 여 인증 및 권한 부여를 지원 합니다. Azure AD를 사용 하 여 요청을 권한 부여 하는 것이 뛰어난 보안과 사용 편의성을 위해 권장 됩니다. 자세한 내용은 [Azure Active Directory를 사용 하 여 Azure blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)를 참조 하세요.
- **Azure Files에 대 한 SMB를 통한 Azure AD 권한 부여.** Azure Files은 Azure Active Directory Domain Services (Azure AD DS) 또는 온-프레미스 Active Directory Domain Services (미리 보기)를 통해 SMB (서버 메시지 블록)를 통한 id 기반 권한 부여를 지원 합니다. 도메인에 가입 된 Windows Vm은 Azure AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 자세한 내용은 [SMB 액세스를 위한 id 기반 인증 지원 Azure Files 개요](../files/storage-files-active-directory-overview.md) 및 [Azure Files 배포에 대 한 계획](../files/storage-files-planning.md#identity)을 참조 하세요.
- **공유 키를 사용 하는 권한 부여입니다.** Azure Storage Blob, 파일, 큐 및 테이블 서비스는 공유 키를 사용 하 여 권한 부여를 지원 합니다. 공유 키 인증을 사용 하는 클라이언트는 저장소 계정 액세스 키를 사용 하 여 서명 된 모든 요청과 함께 헤더를 전달 합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.
- **SAS (공유 액세스 서명)를 사용 하는 권한 부여** SAS (공유 액세스 서명)는 저장소 리소스에 대 한 URI에 추가할 수 있는 보안 토큰을 포함 하는 문자열입니다. 보안 토큰은 권한 및 액세스 간격과 같은 제약 조건을 캡슐화 합니다. 자세한 내용은 [SAS (공유 액세스 서명) 사용](storage-sas-overview.md)을 참조 하세요.
- **컨테이너 및 blob에 대 한 익명 액세스.** 컨테이너 및 해당 blob은 공개적으로 사용할 수 있습니다. 컨테이너 또는 blob을 공용으로 지정 하면 누구나이를 익명으로 읽을 수 있습니다. 인증이 필요 하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.

## <a name="encryption"></a>암호화

핵심 저장소 서비스에는 두 가지 기본 암호화 종류를 사용할 수 있습니다. 보안 및 암호화에 대한 자세한 내용은 [Azure Storage 보안 가이드](../blobs/security-recommendations.md)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

Azure Storage 암호화는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호 합니다. Azure Storage는 저장소 계정에 유지 하기 전에 모든 데이터를 자동으로 암호화 하 고 검색 하기 전에 암호를 해독 합니다. 암호화, 암호 해독 및 키 관리 프로세스는 사용자에 게 투명 합니다. 또한 고객은 Azure Key Vault를 사용 하 여 자신의 키를 관리 하도록 선택할 수 있습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)를 참조하세요.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

Azure Storage 클라이언트 라이브러리는 네트워크를 통해 전송 하기 전에 클라이언트 라이브러리에서 데이터를 암호화 하 고 응답을 해독 하는 메서드를 제공 합니다. 클라이언트 쪽 암호화를 통해 암호화 된 데이터는 Azure Storage 하 여 미사용 에서도 암호화 됩니다. 클라이언트 쪽 암호화에 대 한 자세한 내용은 [Azure Storage 용 .net을 사용 하는 클라이언트 쪽 암호화](storage-client-side-encryption.md)를 참조 하세요.

## <a name="redundancy"></a>중복

데이터를 지속적으로 유지 하기 위해 Azure Storage는 데이터의 복사본을 여러 개 저장 합니다. 스토리지 계정을 설정할 때 중복 옵션을 선택합니다. 자세한 내용은 [Azure Storage 중복성](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json)을 참조하세요.

## <a name="transfer-data-to-and-from-azure-storage"></a>Azure Storage 간 데이터 전송

Azure Storage에서 데이터를 이동하는 몇 가지 옵션이 있습니다. 선택하는 옵션은 데이터 세트의 크기와 네트워크 대역폭에 따라 달라집니다. 자세한 내용은 [데이터 전송을 위한 Azure 솔루션 선택](storage-choose-data-transfer-solution.md)을 참조하세요.

## <a name="pricing"></a>가격 책정

데이터가 저장 및 액세스되는 방식을 결정할 때 관련 비용도 고려해야 합니다. 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

## <a name="storage-apis-libraries-and-tools"></a>Storage API, 라이브러리 및 도구

HTTP/HTTPS 요청을 수행할 수 있는 모든 언어로 저장소 계정의 리소스에 액세스할 수 있습니다. 또한 핵심 Azure Storage 서비스는 널리 사용 되는 여러 언어를 위한 프로그래밍 라이브러리를 제공 합니다. 이 라이브러리는 동기/비동기 호출, 작업 일괄 처리, 예외 관리, 자동 재시도, 작업자 동작 등과 같은 세부 사항을 처리하여 Azure Storage 작업의 많은 측면을 간소화합니다. 현재 이 라이브러리는 파이프라인의 다른 라이브러리와 함께 다음 언어 및 플랫폼에 대해 사용할 수 있습니다.

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage 데이터 API 및 라이브러리 참조

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [.NET 용 Azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Java/Android 용 Azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Node.js용 Azure Storage 클라이언트 라이브러리 ](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Python 용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-python)
- [PHP 용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php)
- [Ruby 용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-ruby)
- [C + + 용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage 관리 API 및 라이브러리 참조

- [Storage 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/storagerp/)
- [.NET용 Storage 리소스 공급자 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [Storage 서비스 관리 REST API(클래식](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage 데이터 이동 API 및 라이브러리 참조

- [스토리지 Import/Export 서비스 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
- [.NET용 스토리지 데이터 이동 클라이언트 라이브러리](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>도구 및 유틸리티

- [Storage용 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.storage)
- [Storage용 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy 명령줄 유틸리티](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
- [Azure Storage 클라이언트 도구](../storage-explorers.md)
- [Azure 개발자 도구](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>다음 단계

핵심 Azure Storage 서비스를 시작 하 고 실행 하려면 [저장소 계정 만들기](storage-account-create.md)를 참조 하세요.
