---
title: Azure Analysis Services 리소스 및 개체 제한 | Microsoft Docs
description: Azure Analysis Services 리소스 및 개체 제한에 대해 설명합니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0ffbffc788baaffd4a0532c3918ed82cc3eaf5c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023558"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services 리소스 및 개체 제한

이 문서에서는 리소스 및 모델 개체 제한에 대해 설명합니다.

## <a name="tier-limits"></a>계층 제한

### <a name="developer-tier"></a>개발자 계층

이 계층은 평가, 개발 및 테스트 시나리오에 권장됩니다. 단일 계획에는 표준 계층과 동일한 기능이 포함되지만, 처리 능력, QPU 및 메모리 크기가 제한됩니다. 쿼리 복제본 스케일 아웃은 이 계층에 사용할 수 없습니다. 이 계층은 SLA를 제공하지 않습니다.

|계획  |QPU  |메모리(GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>기본 계층

해당 계층은 소규모 테이블 형식 모델, 제한된 사용자 동시성 및 단순한 데이터 새로 고침 요구 사항이 있는 프로덕션 솔루션에 권장됩니다. 쿼리 복제본 스케일 아웃은 이 계층에 ‘사용할 수 없습니다’. 큐브 뷰, 여러 파티션 및 DirectQuery 테이블 형식 모델 기능은 이 계층에서 지원되지 않습니다.  

|계획  |QPU  |메모리(GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>표준 계층

이 계층은 탄력적 사용자 동시성이 필요하고 빠르게 성장하는 데이터 모델이 있는 중요 업무용 프로덕션 애플리케이션에 적합합니다. 근실시간 데이터 모델 업데이트에 고급 데이터 새로 고침을 지원하고, 모든 테이블 형식 모델링 기능을 지원합니다.

|계획  |QPU  |메모리(GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* 일부 지역에서 지원되지 않습니다.  

## <a name="object-limits"></a>개체 제한

이러한 제한은 이론적입니다. 이보다 낮은 수치일 때는 성능이 저하됩니다.

|Object|최대 크기/숫자|  
|------------|----------------------------|  
|인스턴스의 데이터베이스|16,000|  
|데이터베이스 내 테이블 및 열을 합한 수|16,000|  
|테이블의 행|Unlimited<br /><br /> **경고:** 테이블에 있는 단일 열이 1,999,999,997개가 넘는 고유 값을 가질 수 없는 제한 사항이 있습니다.|  
|테이블 내의 계층 구조|15,999|  
|계층 구조 내의 수준|15,999|  
|관계|8,000|  
|모든 테이블의 키 열|15,999|  
|테이블의 측정값|2^31-1 = 2,147,483,647|  
|쿼리에서 반환되는 셀|2^31-1 = 2,147,483,647|  
|원본 쿼리의 레코드 크기|64K|  
|개체 이름의 길이|512자|  


