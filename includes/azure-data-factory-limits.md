---
title: 포함 파일
description: 포함 파일
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 34e828a43567ffc701c1645c55f4ff0352b0e8d3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75772956"
---
Azure Data Factory은 고객 구독이 서로 다른 작업 으로부터 보호 되도록 하기 위해 다음과 같은 기본 제한이 적용 되는 다중 테넌트 서비스입니다. 구독에 대한 최대 제한 값을 높이려면 지원 담당자에 게 문의 하세요.

### <a name="version-2"></a>버전 2

| 리소스 | 기본 제한 | 최대 제한 |
| -------- | ------------- | ------------- |
| Azure 구독의 데이터 팩터리 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 팩터리 내에서 파이프라인, 데이터 집합, 트리거, 연결 된 서비스, 통합 런타임 등의 총 엔터티 수 | 5,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Azure의 총 CPU 코어-한 구독에서 SSIS 통합 런타임 | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 팩터리의 모든 파이프라인에서 공유 되는 데이터 팩터리 당 동시 파이프라인 실행 | 10000  | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| [Azure Integration Runtime 지역별](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독 당 동시 외부 활동 실행 수<br><small>외부 활동은 integration runtime에서 관리 되지만 Databricks, 저장 프로시저, HDInsights, 웹 등을 비롯 한 연결 된 서비스에서 실행 됩니다.</small> | 3000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| [Azure Integration Runtime 지역별](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독 당 동시 파이프라인 작업 실행 수 <br><small>파이프라인 활동은 integration runtime (Lookup, GetMetadata 및 Delete 포함)에서 실행 됩니다.</small>| 1000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| [Azure Integration Runtime 지역별](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 구독 당 동시 작성 작업<br>연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 데이터 미리 보기를 포함 하는 <small>. | 200 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 동시 데이터 통합 단위 [Azure Integration Runtime 지역](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) 당 구독 당<sup>1</sup> 소비| 지역 그룹 1<sup>2</sup>: 6000<br>지역 그룹 2<sup>: 3000</sup><br>지역 그룹 3<sup>2</sup>: 1500 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 컨테이너에 대한 내부 활동을 포함 하는 파이프라인 당 최대 활동 | 40 | 40 |
| 단일 자체 호스팅 통합 런타임에 대해 만들 수 있는 연결 된 통합 런타임 최대 수 | 100 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 파이프라인 당 최대 매개 변수 수 | 50 | 50 |
| ForEach 항목 | 100,000개의 | 100,000개의 |
| ForEach 병렬 처리 | 20 | 50 |
| 파이프라인 당 최대 대기 중인 실행 수 | 100 | 100 |
| 식당 문자 | 8,192 | 8,192 |
| 최소 연속 창 트리거 간격 | 15분 | 15분 |
| 파이프라인 작업 실행에 대한 최대 시간 제한 | 7일 | 7일 |
| 파이프라인 개체에 대한 개체당 바이트 수<sup>3</sup> | 200KB | 200KB |
| 데이터 집합 및 연결 된 서비스 개체에 대한 개체당 바이트<sup>3</sup> | 100KB | 2000 KB |
| 복사 작업 실행 당 데이터 통합 단위<sup>1</sup> | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API 호출 쓰기 | 1200/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| API 호출 읽기 | 12500/h<br/><br/> 이 제한은 Azure Data Factory가 아닌 Azure Resource Manager가 부과합니다. | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 분당 모니터링 쿼리 | 1,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 분당 엔터티 CRUD 작업 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 흐름 디버그 세션의 최대 시간 | 8 시간 | 8 시간 |
| 팩터리 당 동시 데이터 흐름 수 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 사용자 당 팩터리 당 동시 데이터 흐름 디버그 세션 수 | 3 | 3 |
| 데이터 흐름 Azure IR TTL 제한 | 4 시간 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> diu (데이터 통합 단위)는 클라우드-클라우드 복사 작업에 사용 되며, [데이터 통합 단위 (버전 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units)에서 자세히 알아보세요. 요금 청구에 대한 자세한 내용은 [Azure Data Factory 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/)을 참조 하세요.

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 데이터 호환성, 효율성 및 네트워크 송신 비용 절감을 위해 [전역적으로 사용할 수 있습니다](https://azure.microsoft.com/global-infrastructure/services/) . 

| 영역 그룹 | 개 지역 | 
| -------- | ------ |
| 지역 그룹 1 | 미국 중부, 미국 동부, 동 미국, 북부 유럽, 유럽 서부, 미국 서 부, 미국 서 부 2 |
| 영역 그룹 2 | 오스트레일리아 동부, 오스트레일리아 남동쪽, 브라질 남부, 인도 중부, 일본 동부, 미국 Northcentral, 미국 미국, 동남 아시아, 미국 서 부 중부 |
| 영역 그룹 3 | 캐나다 중부, 동아시아, 프랑스 중부, 대한민국 중부, 영국 남부 |

<sup>3</sup> 파이프라인, 데이터 집합 및 연결 된 서비스 개체는 작업의 논리적 그룹화를 나타냅니다. 이러한 개체에 대한 제한은 Azure Data Factory로 이동 및 처리할 수 있는 데이터의 양과는 관련이 없습니다. Data Factory는 페타바이트 데이터를 처리 하도록 크기를 조정 하도록 설계 되었습니다.

### <a name="version-1"></a>버전 1

| **리소스** | **기본 제한** | **최대한도** |
| --- | --- | --- |
| 데이터 팩터리 내의 파이프라인sd |2,500 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 팩터리 내의 데이터 집합 |5,000 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 데이터 집합 당 동시 조각 수 |10 |10 |
| 파이프라인 개체에 대한 개체당 바이트<sup>1</sup> |200KB |200KB |
| 데이터 집합 및 연결 된 서비스 개체에 대한 개체당 바이트<sup>1</sup> |100KB |2000 KB |
| 구독에서 Azure HDInsight 주문형 클러스터 코어<sup>2</sup> |60 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 복사 작업 실행 당 클라우드 데이터 이동 단위<sup>3</sup> |32 |[고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 파이프라인 활동 실행에 대한 재시도 횟수 |1,000 |MaxInt(32비트) |

<sup>1</sup> 파이프라인, 데이터 집합 및 연결 된 서비스 개체는 작업의 논리적 그룹화를 나타냅니다. 이러한 개체에 대한 제한은 Azure Data Factory로 이동 및 처리할 수 있는 데이터의 양과는 관련이 없습니다. Data Factory는 페타바이트 데이터를 처리 하도록 크기를 조정 하도록 설계 되었습니다.

<sup>2</sup> 주문형 HDInsight 코어는 데이터 팩터리를 포함하는 구독 외부에서 할당됩니다. 따라서 이전 제한은 주문형 HDInsight 코어에 대한 Data Factory 적용 되는 코어 한도입니다. Azure 구독과 연결 된 코어 제한과는 다릅니다.

<sup>3</sup> 버전 1에 대한 클라우드 데이터 이동 단위 (dmu)는 클라우드-클라우드 복사 작업에 사용 되며, [클라우드 데이터 이동 단위 (버전 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)에서 자세히 알아보세요. 요금 청구에 대한 자세한 내용은 [Azure Data Factory 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/)을 참조 하세요.

| **리소스** | **기본 하한값** | **최소 제한** |
| --- | --- | --- |
| 일정 간격 |15분 |15분 |
| 재시도 간의 간격 |1초 |1초 |
| 재시도 시간 제한 값 |1초 |1초 |

#### <a name="web-service-call-limits"></a>웹 서비스 호출 제한
Azure Resource Manager에는 API 호출 제한이 있습니다. [Azure 리소스 관리자 API 제한](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)범위 안의 속도로 API 호출을 수행할 수 있습니다.
