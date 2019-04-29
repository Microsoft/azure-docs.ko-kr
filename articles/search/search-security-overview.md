---
title: 보안 및 데이터 프라이버시 - Azure Search
description: Azure Search는 SOC 2, HIPAA 및 기타 인증을 준수합니다. 연결 및 데이터 암호화, 인증, ID는 Azure Search 필터의 사용자 및 그룹 보안 식별자를 통해 액세스합니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11b2fb5a246dfa8f5b1295a11cc57de36120898e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283427"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Azure Search의 보안 및 데이터 프라이버시

개인 콘텐츠를 안전하게 보호하기 위해 포괄적인 보안 기능 및 액세스 제어가 Azure Search에 기본적으로 제공됩니다. 이 문서에서는 Azure Search에 내장된 보안 기능 및 표준 규정 준수를 설명합니다.

Azure Search 보안 아키텍처는 물리적 보안, 암호화된 전송, 암호화된 저장소 및 플랫폼 전체 표준 준수를 모두 제공합니다. 운영상 Azure Search는 인증된 요청을 허용합니다. 필요에 따라 보안 필터를 통해 콘텐츠에 사용자당 액세스 제어를 추가할 수 있습니다. 이 문서에서는 각 계층의 보안에 대해 다루지만 주로 Azure Search에서 데이터 및 작업을 보호하는 방법을 중점적으로 설명합니다.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>표준 준수: ISO 27001, SOC 2, HIPAA

Azure Search는 [2018년 6월에 발표](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)된 것과 같이 다음 표준에 대한 인증을 받았습니다. 

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Type 2 준수](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) 전체 보고서를 보려면 [Azure - 및 Azure Government SOC 2 Type II 보고서](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)로 이동하세요. 
+ [HIPAA(Health Insurance Portability and Accountability Act)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP(21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS Level 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australia IRAP Unclassified DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

표준 규정 준수는 일반적으로 사용 가능한 기능에 적용됩니다. 미리 보기 기능은 일반적인 가용성으로 전환될 때 인증되며 엄격한 표준 요구 사항이 있는 솔루션에는 사용하면 안 됩니다. 규정 준수 인증은 [Microsoft Azure 규정 준수 개요](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 및 [보안 센터](https://www.microsoft.com/en-us/trustcenter)에 설명되어 있습니다. 

## <a name="encrypted-transmission-and-storage"></a>암호화된 전송 또는 저장

암호화는 전송을 통해 연결에서 Azure Search에 저장된 인덱싱된 데이터로 전체 인덱싱 파이프라인에 확장됩니다.

| 보안 계층 | 설명 |
|----------------|-------------|
| 전송 중 암호화 <br>(HTTPS/SSL/TLS) | Azure Search는 HTTPS 포트 443에서 수신 대기합니다. 플랫폼 전체에서 Azure 서비스에 대한 연결이 암호화됩니다. <br/><br/>모든 클라이언트-서비스 Azure Search 상호 작용에는 SSL/TLS 1.2가 지원됩니다.  서비스에 SSL을 연결하려면 TLSv1.2를 사용해야 합니다.|
| 휴지 상태의 암호화 | 암호화는 인덱싱 시간 완료 또는 인덱스 크기에 측정 가능한 영향을 주지 않고 인덱싱 프로세스에 완벽하게 내부화됩니다. 완전하게 암호화되지 않은 인덱스(2018년 1월 전에 생성됨)에 대한 증분 업데이트를 비롯한 모든 인덱싱에서 자동으로 수행됩니다.<br><br>내부적으로 암호화는 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 [Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 기반으로 합니다.|

암호화는 Microsoft에서 내부적으로 관리하고 전역적으로 적용되는 인증서 및 암호화 키를 사용하여 Azure Search에 포함됩니다. 포털에서 또는 프로그래밍 방식으로 암호화를 켜고 끄거나, 고유한 키를 관리하고 대체하거나, 암호화 설정을 볼 수 없습니다. 

미사용 암호화는 2018년 1월 24일에 발표되었으며 공유(체험) 서비스를 포함하여 모든 지역에서 모든 서비스 계층에 적용됩니다. 전체 암호화의 경우 해당 날짜 이전에 만든 인덱스를 삭제하고 암호화를 수행하기 위해 다시 빌드해야 합니다. 그렇지 않으면 1월 24일 이후에 추가된 새 데이터만이 암호화됩니다.

## <a name="azure-wide-user-access-controls"></a>Azure 전체 사용자 액세스 제어

Azure 전체에서 여러 보안 메커니즘을 사용할 수 있으며, 따라서 사용자가 만드는 Azure Search 리소스에 자동으로 사용할 수 있습니다.

+ [삭제를 방지하기 위해 구독 또는 리소스 수준 잠금](../azure-resource-manager/resource-group-lock-resources.md)
+ [정보 및 관리 작업에 대한 액세스를 제어하는 RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)

모든 Azure 서비스는 모든 서비스에서 일관된 수준의 액세스를 설정하기 위해 RBAC(역할 기반 액세스 제어)를 지원합니다. 예를 들어 관리 키와 같은 중요한 데이터는 소유자 및 참가자 역할에서만 확인할 수 있는 반면 서비스 상태는 역할에 관계없이 모든 구성원이 확인할 수 있습니다. RBAC는 소유자, 참가자 및 독자 역할을 제공합니다. 기본적으로 모든 서비스 관리자는 소유자 역할의 구성원입니다.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>서비스 액세스 및 인증

Azure Search이 Azure 플랫폼의 보안 보호 기능을 상속하는 동시에 고유한 키 기반 인증도 제공합니다. api-key는 임의로 생성된 숫자 및 문자로 구성된 문자열입니다. 키의 형식(관리자 또는 쿼리)은 액세스 수준을 결정합니다. 유효한 키를 제출하면 요청이 신뢰할 수 있는 엔터티에서 시작되었다는 증명으로 간주됩니다. 

검색 서비스에 대한 두 가지 액세스 수준이 있으며, 다음과 같은 두 가지 유형의 키를 통해 설정됩니다.

* 관리자 액세스(서비스에 대한 모든 읽기-쓰기 작업에 유효)
* 쿼리 (인덱스의 문서 컬렉션에 대 한 쿼리와 같은 읽기 전용 작업에 유효) 액세스

*관리자 키*는 서비스가 프로비저닝될 때 만들어집니다. 두 개의 관리 키가 있으며 단순하게 유지하기 위해 *주* 및 *보조*로 지정되지만 실제로는 서로 바꿔 사용할 수 있습니다. 각 서비스에는 서비스에 대한 액세스 권한을 잃지 않고 롤오버할 수 있는 두 개의 관리 키가 있습니다. 할 수 있습니다 [다시 생성 하는 관리자 키](search-security-api-keys.md#regenerate-admin-keys) 주기적으로 Azure 보안 당 수 있지만 모범 사례를 추가할 수 없습니다 총 관리 키 수입니다. Search 서비스 당 두 개의 관리자 키는 최대가 있습니다.

*쿼리 키* 필요에 따라 생성 되 고 쿼리를 실행 하는 클라이언트 응용 프로그램에 대 한 설계 되었습니다. 최대 50개까지 쿼리 키를 만들 수 있습니다. 응용 프로그램 코드에서 특정 인덱스의 문서 컬렉션에 대 한 읽기 전용 액세스를 허용 하도록 검색 URL 및 쿼리 api 키를 지정 합니다. 동시에, 엔드포인트, 읽기 전용 액세스를 위한 api-key 및 대상 인덱스는 클라이언트 애플리케이션에서 연결의 액세스 수준 및 범위를 정의합니다.

인증은 필수 키, 작업 및 개체로 구성된 각 요청에 필요합니다. 함께 연결하는 경우 두 개의 사용 권한 수준(전체 및 읽기 전용) 및 컨텍스트(예: 인덱스에서의 쿼리 작업)는 서비스 작업에 전체 스펙트럼 보안을 충분히 제공할 수 있습니다. 키에 대 한 자세한 내용은 [api-key 만들기 및 관리](search-security-api-keys.md)를 참조하세요.

## <a name="index-access"></a>액세스 인덱싱

Azure Search에서 개별 인덱스는 보안 개체가 아닙니다. 대신 인덱스에 대한 액세스는 작업의 컨텍스트와 함께 서비스 계층(읽기 또는 쓰기 액세스)으로 결정됩니다.

최종 사용자 액세스의 경우 쿼리 키를 사용하여 연결하도록 쿼리 요청을 구성할 수 있습니다. 여기서는 모든 요청을 읽기 전용으로 설정하고 앱에서 사용하는 특정 인덱스를 포함합니다. 쿼리 요청에서 동시에 인덱스를 조인하거나 여러 인덱스에 액세스한다는 개념은 없으므로 모든 요청은 정의에 따라 단일 인덱스를 대상으로 지정합니다. 따라서 쿼리 요청 자체의 구조(키 및 단일 대상 인덱스)는 보안 경계를 정의합니다.

인덱스에 대한 관리자 및 개발자 액세스는 구분되지 않습니다. 둘 다 서비스에서 관리되는 개체를 만들고, 삭제하고, 업데이트하는 쓰기 액세스 권한이 필요합니다. 서비스에 대한 관리자 키가 있는 모든 사용자는 동일한 서비스에서 모든 인덱스를 읽기, 수정 또는 삭제할 수 있습니다. 인덱스를 우발적이거나 악의적인 삭제로부터 보호하기 위해 코드 자산에 대한 내부 원본을 제어하여 원치 않는 삭제나 수정을 되돌리는 문제를 해결합니다. Azure Search에는 가용성을 보장하기 위해 클러스터 내에서 장애 조치 기능이 있지만 인덱스를 만들거나 로드하는 데 사용되는 전용 코드를 저장하거나 실행하지 않습니다.

인덱스 수준에서 보안 경계를 요구하는 다중 테넌트 솔루션의 경우 이러한 솔루션에는 일반적으로 중간 계층이 포함됩니다. 고객은 인덱스 격리를 처리하기 위해 이를 사용합니다. 다중 테넌트 사용 사례에 대한 자세한 내용은 [다중 테넌트 SaaS 애플리케이션 및 Azure Search에 대한 디자인 패턴](search-modeling-multitenant-saas-applications.md)을 참조하세요.

## <a name="admin-access"></a>관리자 액세스

[역할 기반 액세스 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 서비스 및 해당 콘텐츠를 통해 컨트롤에 액세스할 수 있는지 여부를 결정 합니다. 포털 또는 PowerShell 인 경우 소유자 또는 참가자 Azure Search 서비스에서 사용할 수 있습니다 **Az.Search** 모듈 만들기, 업데이트 또는 서비스에서 개체를 삭제 합니다. 사용할 수도 있습니다는 [Azure Search 관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)합니다.

## <a name="user-access"></a>사용자 액세스

기본적으로 인덱스에 대한 사용자 액세스는 쿼리 요청의 액세스 키에 따라 결정됩니다. 대부분의 개발자는 클라이언트 쪽 검색 요청에 대한 [*쿼리 키*](search-security-api-keys.md)를 만들어서 할당합니다. 쿼리 키는 인덱스의 모든 콘텐츠에 대한 읽기 액세스 권한을 부여합니다.

콘텐츠에 대한 세밀한 사용자별 제어가 필요한 경우 쿼리에 대한 보안 필터를 빌드하고, 지정된 보안 ID와 연결된 문서를 반환할 수 있습니다. 미리 정의된 역할 및 역할 할당 대신 ID 기반 액세스 제어는 ID에 따라 문서 및 콘텐츠의 검색 결과를 잘라내는 *필터*로 구현됩니다. 다음 표에서는 권한이 없는 콘텐츠의 검색 결과를 잘라내는 방법에 대한 두 가지 방법을 설명합니다.

| 접근 방식 | 설명 |
|----------|-------------|
|[ID 필터에 따라 보안 조정](search-security-trimming-for-azure-search.md)  | 사용자 ID 액세스 제어를 구현하기 위한 기본 워크플로를 문서화합니다. 인덱스에 보안 식별자를 추가하는 방법을 다루고 금지된 콘텐츠의 결과를 잘라내는 해당 필드에 대한 필터링을 설명합니다. |
|[Azure Active Directory ID에 따라 보안 조정](search-security-trimming-for-azure-search-with-aad.md)  | 이 문서는 이전 문서에서 확장되어 Azure 클라우드 플랫폼에서 제공하는 [체험 서비스](https://azure.microsoft.com/free/) 중 하나인 AAD(Azure Active Directory)에서 ID를 검색하는 단계를 제공합니다. |

## <a name="table-permissioned-operations"></a>표: 권한이 지정된 작업

다음 표에서는 Azure Search에서 허용되는 작업 및 특정 작업에 대한 액세스의 잠금을 해제하는 키를 요약합니다.

| 작업(Operation) | 권한 |
|-----------|-------------------------|
| 서비스 만들기 | Azure 구독 소유자|
| 서비스 크기 조정 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자  |
| 서비스 삭제 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자 |
| 서비스에서 개체를 만들고, 수정하고, 삭제합니다. <br>인덱스 및 구성 요소 부분(분석기 정의, 점수 매기기 프로필, CORS 옵션 포함), 인덱서, 데이터 원본, 동의어, 확인기 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자  |
| 인덱스 쿼리 | 관리자 또는 쿼리 키(RBAC 적용되지 않음) |
| 통계, 개수 및 개체의 목록을 반환하는 등 쿼리 시스템 정보입니다. | 관리자 키, 리소스에 대한 RBAC(소유자, 참가자, 독자) |
| 관리자 키 관리 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자 |
| 쿼리 키 관리 |  관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자  |

## <a name="physical-security"></a>물리적 보안

Microsoft 데이터 센터는 업계 최고의 물리적 보안을 제공하고 광범위한 포트폴리오의 표준 및 규정을 준수합니다. 자세한 내용을 보려면 [글로벌 데이터 센터](https://www.microsoft.com/cloud-platform/global-datacenters) 페이지로 이동하거나 데이터 센터 보안에 대한 짧은 비디오를 시청하세요.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>참고 항목

+ [.NET 시작(관리자 키를 사용하여 인덱스를 만드는 방법을 보여줍니다.)](search-create-index-dotnet.md)
+ [REST 시작(관리자 키를 사용하여 인덱스를 만드는 방법을 보여줍니다.)](search-create-index-rest-api.md)
+ [Azure Search 필터를 사용하여 ID 기반 액세스 제어](search-security-trimming-for-azure-search.md)
+ [Azure Search 필터를 사용하여 Active Directory ID 기반 액세스 제어](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure Search의 필터](search-filters.md)