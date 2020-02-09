---
title: 데이터 흐름 문제 해결
description: Azure Data Factory에서 데이터 흐름 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: 901868da8ed859a846a507557d383db760f297c9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029523"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure Data Factory 데이터 흐름 문제 해결

이 문서에서는 Azure Data Factory의 데이터 흐름에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-실행자-SourceInvalidPayload
- **메시지**: 컨테이너가 없으므로 데이터 미리 보기, 디버그 및 파이프라인 데이터 흐름을 실행 하지 못했습니다.
- **원인**: 데이터 집합에 저장소에 없는 컨테이너가 포함 된 경우
- **권장 사항**: 데이터 집합에서 참조 되는 컨테이너가 존재 하거나 액세스할 수 있는지 확인 합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-SystemImplicitCartesian

- **메시지**: 내부 조인에 대한 암시적 데카르트 제품은 지원 되지 않습니다. 대신 CROSS join을 사용 하십시오. 조인에 사용 되는 열은 행에 대한 고유 키를 만들어야 합니다.
- **원인**: 논리 요금제 간의 내부 조인에 대한 암시적 직교 곱을 지원 하지 않습니다. 조인에 사용 된 열이 고유 키를 만드는 경우
- **권장 사항**: 같지 않음 기반 조인의 경우 크로스 조인을 사용 하도록 선택 해야 합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-SystemInvalidJson

- **메시지**: JSON 구문 분석 오류, 지원 되지 않는 인코딩 또는 여러 줄
- **원인**: json 파일의 가능한 문제: 지원 되지 않는 인코딩, 손상 된 바이트 또는 여러 중첩 된 줄에서 단일 문서로 json 원본 사용
- **권장 사항**: JSON 파일의 인코딩이 지원 되는지 확인 합니다. JSON 데이터 집합을 사용 하는 원본 변환에서 ' JSON 설정 '을 확장 하 고 ' 단일 문서 '를 설정 합니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-BroadcastTimeout

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60 초 내에 데이터를 생성 하 고 작업 실행에서 300 초 내에 데이터를 생성 하는지 확인 합니다.
- **원인**: 브로드캐스트는 디버그 실행에서 기본 시간 제한이 60 초이 고 작업 실행에서 300 초입니다. 브로드캐스트에 대해 선택한 스트림은이 한도 내에 데이터를 생성 하는 데 너무 많은 것 같습니다.
- **권장 사항**: 처리량이 60 초를 초과 하는 경우 대량 데이터 스트림을 브로드캐스트하는 것이 좋습니다. 대신 브로드캐스트할 더 작은 스트림을 선택 합니다. 일반적으로 규모가 많은 SQL/DW 테이블 및 소스 파일은 잘못 된 후보입니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-변환

- **메시지**: 잘못 된 문자로 인해 날짜 또는 시간으로 변환 하지 못했습니다.
- **원인**: 데이터가 예상 된 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식 사용

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-실행-InvalidColumn

- **메시지**: 쿼리에 열 이름을 지정 해야 합니다. SQL 함수를 사용 하는 경우 별칭을 설정 합니다.
- **원인**: 열 이름이 지정 되지 않았습니다.
- **권장 사항**: min ()/max () 등의 SQL 함수를 사용 하는 경우 별칭을 설정 합니다.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침

1. 데이터 집합 연결의 상태를 확인 합니다. 각 원본 및 싱크 변환에서 사용 중인 각 데이터 집합에 대한 연결 된 서비스를 방문 하 고 연결을 테스트 합니다.
1. 데이터 흐름 디자이너에서 파일 및 테이블 연결의 상태를 확인 합니다. 디버그를 전환 하 고 원본 변환에서 데이터 미리 보기를 클릭 하 여 데이터에 액세스할 수 있는지 확인 합니다.
1. 모든 항목이 데이터 미리 보기에서 양호 하면 파이프라인 디자이너로 이동 하 여 파이프라인 활동에 데이터 흐름을 배치 합니다. 종단 간 테스트를 위한 파이프라인을 디버깅 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대한 자세한 내용은 다음 리소스를 참조 하세요.
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
