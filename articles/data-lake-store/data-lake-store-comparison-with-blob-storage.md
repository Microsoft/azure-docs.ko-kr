---
title: Blob 저장소와 Azure Data Lake Storage Gen1 비교
description: 빅 데이터 처리의 몇 가지 주요 측면에 대 한 Azure Data Lake Storage Gen1와 Azure Blob Storage 간의 차이점에 대해 알아봅니다.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 6662beec47d663f92e453f52d190e41518108b0d
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191486"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Azure Data Lake Storage Gen1과 Azure Blob Storage 비교

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

이 문서의 표에서는 빅 데이터 처리의 일부 주요 측면을 따라 Azure Data Lake Storage Gen1과 Azure Blob Storage 간의 차이점을 요약합니다. Azure Blob Storage는 다양한 스토리지 시나리오를 위해 설계된 확장성 있는 범용 개체 저장소입니다. Azure Data Lake Storage Gen1은 빅 데이터 분석 워크로드에 대해 최적화된 하이퍼 스케일 리포지토리입니다.

| Category | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| -------- | ---------------------------- | ------------------ |
| 용도 |빅 데이터 분석 워크로드에 대해 최적화된 스토리지 |빅 데이터 분석을 포함한 다양한 스토리지 시나리오에 대한 범용 개체 스토리지 |
| 사용 사례 |Batch, 대화형, 스트리밍 분석 및 로그 파일, IoT 데이터, 클릭 스트림, 대형 데이터 세트 등과 같은 기계 학습 데이터 |애플리케이션 백 엔드, 백업 데이터, 스트리밍용 미디어 스토리지 및 범용 데이터 등과 같은 모든 종류의 텍스트 또는 이진 데이터. 추가적으로 Batch, 대화형, 스트리밍 분석 및 로그 파일, IoT 데이터, 클릭 스트림, 대형 데이터 세트 등과 같은 기계 학습 데이터 등의 분석 워크로드 전면 지원 |
| 주요 개념 |Data Lake Storage Gen1 계정은 폴더를 포함하며, 이 폴더는 파일로 저장된 데이터를 포함함 |Storage 계정은 컨테이너를 가지며, 이 컨테이너는 blob 형식의 데이터를 가짐 |
| 구조 |계층적 파일 시스템 |단일 구조 네임스페이스를 가진 개체 저장소 |
| API |HTTPS를 통한 REST API |HTTP/HTTPS를 통한 REST API |
| 서버 쪽 API |[WebHDFS 호환 REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop 파일 시스템 클라이언트 |예 |예 |
| 데이터 작업 - 인증 |[Azure Active Directory ID](../active-directory/develop/authentication-scenarios.md) |공유 비밀 기반 - [계정 액세스 키](../storage/common/storage-account-keys-manage.md) 및 [공유 액세스 서명 키](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| 데이터 작업 - 인증 프로토콜 |OAuth 2.0. 호출은 Azure Active Directory가 발급한 유효한 JWT(JSON Web Token)를 포함해야 함 |HMAC (해시 기반 MAC(메시지 인증 코드)). 호출은 HTTP 요청 일부를 통해 Base64 인코딩된 SHA-256 해시를 포함해야 합니다. |
| 데이터 작업 - 인증 |POSIX Access Control 목록(ACL)입니다.  Azure Active Directory ID를 기반으로 하는 ACL은 파일 및 폴더 수준에서 설정할 수 있습니다. |계정 수준 인증의 경우 - [계정 액세스 키](../storage/common/storage-account-keys-manage.md) 사용.<br>계정, 컨테이너 또는 Blob 권한 부여의 경우 - [공유 액세스 서명 키](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 사용. |
| 데이터 작업 - 감사 |사용 가능. 자세한 내용은 [여기](data-lake-store-diagnostic-logs.md) 를 참조 하세요. |사용 가능 |
| 미사용 암호화 데이터 |<ul><li>투명한, 서버 쪽</li> <ul><li>서비스 관리 키 사용</li><li>Azure KeyVault의 고객 관리 키 사용</li></ul></ul> |<ul><li>투명한, 서버 쪽</li> <ul><li>서비스 관리 키 사용</li><li>Azure KeyVault의 고객 관리 키 사용(미리 보기)</li></ul><li>클라이언트 쪽 암호화</li></ul> |
| 관리 작업 (예: 계정 만들기) |계정 관리를 위한 [AZURE RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md) |계정 관리를 위한 [AZURE RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md) |
| 개발자 SDK |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| 분석 워크로드 성능 |병렬 분석 워크로드에 대해 최적화된 성능입니다. 높은 처리량 및 IOPS. |병렬 분석 워크로드에 대해 최적화된 성능입니다. |
| 크기 한도 |계정 크기, 파일 크기 또는 파일 수에 대 한 제한 없음 |특정 한도에 대해서는 [standard storage 계정에 대 한 확장성 목표](../storage/common/scalability-targets-standard-account.md) 및 [Blob storage의 확장성 및 성능 목표](../storage/blobs/scalability-targets.md)를 참조 하세요. [Azure 지원](https://azure.microsoft.com/support/faq/)에 연결해 사용할 수 있는 더 큰 계정 한도 |
| 지리적 중복 |로컬 중복 (한 Azure 지역에 있는 여러 데이터 복사본) |로컬 중복(LRS), 영역 중복(ZRS), 전역 중복(GRS), 읽기 액세스 전역 중복(RA-GRS). 자세한 내용은 [여기](../storage/common/storage-redundancy.md) 를 참조 하세요. |
| 서비스 상태 |일반 공급 |일반 공급 |
| 국가별 가용성 |[여기](https://azure.microsoft.com/regions/#services) 를 참조 하세요. |모든 Azure 지역에서 사용가능 |
| 가격 |[가격 책정](https://azure.microsoft.com/pricing/details/data-lake-store/) 참조 |[가격 책정](https://azure.microsoft.com/pricing/details/storage/) 참조 |
