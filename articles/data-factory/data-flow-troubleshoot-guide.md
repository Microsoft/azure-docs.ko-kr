---
title: 데이터 흐름 문제 해결
description: Azure Data Factory의 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/16/2020
ms.openlocfilehash: 0a691b562ebf030712eb0c13a688ea9a52fdb164
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263472"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure Data Factory의 데이터 흐름 문제 해결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory의 데이터 흐름에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-Executor-SourceInvalidPayload
- **메시지**: 컨테이너가 없어 데이터 미리 보기, 디버그, 파이프라인 데이터 흐름 실행 실패
- **원인**: 데이터 세트에 스토리지에 없는 컨테이너가 포함된 경우
- **권장 사항**: 데이터 세트에서 참조되는 컨테이너가 있거나 액세스할 수 있는지 확인합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-Executor-SystemImplicitCartesian

- **메시지**: INNER 조인에 대한 암시적 카테시안 제품은 지원되지 않습니다. 대신 CROSS 조인을 사용하세요. 조인에 사용된 열은 행에 대해 고유 키를 만들어야 합니다.
- **원인**: 논리 계획 사이의 INNER 조인에 대한 암시적 카테시안 제품은 지원되지 않습니다. 조인에 사용된 열이 고유 키를 만들 경우 관계의 양쪽에서 하나 이상의 열이 필요합니다.
- **권장 사항**: 비등가 조인의 경우 CUSTOM CROSS 조인을 선택해야 합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-Executor-SystemInvalidJson

- **메시지**: JSON 구문 분석 오류, 지원되지 않는 인코딩 또는 여러 줄
- **원인**: JSON 파일의 가능한 문제: 지원되지 않은 인코딩, 손상된 바이트, 여러 중첩 줄에 단일 문서로 JSON 원본 사용
- **권장 사항**: JSON 파일의 인코딩이 지원되는지 확인합니다. JSON 데이터 세트를 사용하는 원본 변환에서 ‘JSON 설정’을 확장하고 ‘단일 문서’를 설정합니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-Executor-BroadcastTimeout

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60초, 작업 실행에서 300초 안에 데이터를 생성하는지 확인
- **원인**: 브로드캐스트의 기본 시간 제한은 디버그 실행에서 60초, 작업 실행에서 300초입니다. 브로드캐스트에 대해 선택한 스트림이 너무 커 이 제한 안에 데이터를 생성하지 못하는 것 같습니다.
- **권장 사항**: 데이터 흐름 변환에서 조인, 존재 및 조회에 대해 최적화 탭을 선택합니다. 브로드캐스트의 기본 옵션은 "자동"입니다. 이 항목을 설정하거나 브로드캐스트 왼쪽 또는 오른쪽의 “고정”에서 수동으로 설정할 경우 더 큰 Azure Integration Runtime 구성을 설정하거나 브로드캐스트를 끌 수 있습니다. 데이터 흐름에서 최상의 성능을 내려면 Spark가 “자동”을 사용하여 브로드캐스트하고 메모리 최적화 Azure IR을 사용하도록 허용하는 것이 좋습니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-Executor-Conversion

- **메시지**: 잘못된 문자로 인해 날짜 또는 시간 변환 실패
- **원인**: 데이터가 예상된 형식이 아님
- **권장 사항**: 올바른 데이터 형식 사용

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-Executor-InvalidColumn

- **메시지**: 쿼리에 열 이름을 지정해야 합니다. SQL 함수를 사용하는 경우 별칭 설정
- **원인**: 열 이름을 지정하지 않음
- **권장 사항**: Min()/max() 등의 SQL 함수를 사용하는 경우 별칭 설정

### <a name="error-code-getcommand-outputasync-failed"></a>오류 코드: GetCommand OutputAsync 실패

- **메시지**: 데이터 흐름 디버그 및 데이터 미리 보기 중: GetCommand OutputAsync 실패 ...
- **원인**: 백엔드 서비스 오류입니다. 작업을 다시 시도하고 디버그 세션을 다시 시작할 수도 있습니다.
- **권장 사항**: 다시 시도 및 다시 시작을 통해 문제가 해결되지 않으면 고객 지원팀에 문의하세요.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>오류 코드: 예기치 않은 예외로 실행 실패

- **메시지**: 데이터 흐름 활동 실행 중: 예기치 않은 예외로 실행에 실패했습니다.
- **원인**: 백엔드 서비스 오류입니다. 작업을 다시 시도하고 디버그 세션을 다시 시작할 수도 있습니다.
- **권장 사항**: 다시 시도 및 다시 시작을 통해 문제가 해결되지 않으면 고객 지원팀에 문의하세요.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>오류 코드: 데이터 미리 보기 디버그 조인에 출력 데이터가 없습니다.

- **메시지**: 너무 적은 수의 샘플링으로 인해 발생할 수 있는 null 값 또는 누락 된 값이 많습니다. 디버그 행 제한을 업데이트 하 고 데이터를 새로 고쳐 보세요.
- **원인**: 조인 조건이 행과 일치 하지 않거나 데이터 미리 보기 중에 많은 null을 생성 했습니다.
- **권장 사항**: 디버그 설정으로 이동 하 여 원본 행 제한의 행 수를 늘립니다. 더 많은 데이터를 처리 하기에 충분 한 데이터 흐름 클러스터가 있는 select 및 Azure IR 있는지 확인 합니다.


## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침

1. 데이터 세트 연결의 상태를 확인합니다. 각 원본 및 싱크 변환에서 사용 중인 각각의 데이터 세트에 대해 연결된 서비스를 방문하고 연결을 테스트합니다.
1. 데이터 흐름 디자이너에서 파일과 테이블의 연결 상태를 확인합니다. 디버그로 전환하고 원본 변환에서 데이터 미리 보기를 클릭하여 데이터에 액세스할 수 있는지 확인합니다.
1. 데이터 미리 보기에서 모든 항목이 양호하면 파이프라인 디자이너로 이동하여 파이프라인 활동에 데이터 흐름을 배치합니다. 엔드투엔드 테스트에 대해 파이프라인을 디버깅합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.
*  [Data Factory 블로그](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
