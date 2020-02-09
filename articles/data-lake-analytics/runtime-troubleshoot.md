---
title: Azure Data Lake Analytics U SQL 런타임 오류 문제를 해결 하는 방법
description: U-SQL 런타임 오류 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648455"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>런타임 변경으로 인 한 U-SQL 런타임 오류 문제를 해결 하는 방법 알아보기

컴파일러, 최적화 프로그램 및 작업 관리자를 비롯 한 Azure Data Lake U-sql 런타임은 사용자의 U-SQL 코드를 처리 합니다.

## <a name="choosing-your-u-sql-runtime-version"></a>사용자의 U-SQL 런타임 버전 선택

Visual Studio, ADL SDK 또는 Azure Data Lake Analytics 포털에서 U-SQL 작업을 제출 하면 작업에서 현재 사용 가능한 기본 런타임을 사용 하 게 됩니다. 새 버전의 U-SQL 런타임은 정기적으로 출시 되며, 사소한 업데이트와 보안 픽스를 모두 포함 합니다.

사용자 지정 런타임 버전을 선택할 수도 있습니다. 새 업데이트를 시도 하려고 하기 때문에 이전 버전의 런타임을 유지 하거나 일반적인 새 업데이트를 기다릴 수 없는 보고 된 문제에 대한 핫픽스를 제공 해야 합니다.

> [!CAUTION]
> 기본값과 다른 런타임을 선택 하면 U-SQL 작업이 중단 될 수 있습니다. 이러한 다른 버전은 테스트에만 사용 합니다.

드문 경우 지만 Microsoft 지원 다른 버전의 런타임을 계정의 기본값으로 고정할 수 있습니다. 이 pin은 가능한 한 빨리 되돌려야 합니다. 해당 버전에 고정 된 상태를 유지 하는 경우 나중에 만료 됩니다.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>작업 모니터링 U-SQL 런타임 버전

Visual Studio의 작업 브라우저 또는 Azure Portal의 작업 기록을 통해 계정 작업 기록에서 이전 작업을 사용한 런타임 버전의 기록을 볼 수 있습니다.

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **모든 작업 보기**를 선택 합니다. 계정에서 모든 활성 및 최근에 완료 된 작업 목록이 표시 됩니다.
3. 필요에 따라 **필터**를 클릭하여 **시간 범위**, **작업 이름** 및 **작성자** 값을 기준으로 작업을 찾을 수 있습니다.
4. 완료 된 작업에 사용 된 런타임을 볼 수 있습니다.

![이전 작업의 런타임 버전 표시](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

사용 가능한 런타임 버전은 시간이 지남에 따라 변경 됩니다. 기본 런타임을 항상 "default" 라고 하며, 특정 시간 동안 이전 런타임을 사용할 수 있도록 유지 하 고 다양 한 이유로 특수 런타임을 사용할 수 있도록 합니다. 명시적으로 명명 된 런타임은 일반적으로 다음 형식을 따릅니다. 기울임꼴은 변수 부분에 사용 되 고 []는 선택적 부분을 나타냅니다.

release_YYYYMMDD_adl_buildno [_modifier]

예를 들어 release_20190318_adl_3394512_2는 3 월 18 2019 일의 런타임 릴리스의 빌드 3394512에 대한 두 번째 버전을 의미 하 고, release_20190318_adl_3394512_private는 동일한 릴리스의 개인 빌드를 의미 합니다. 참고: 해당 릴리스에 대한 마지막 체크 인이 수행 된 시간과 공식 릴리스 날짜가 아닐 경우 날짜는와 관련 되어 있습니다.

현재 사용할 수 있는 런타임 버전은 다음과 같습니다.

- release_20190318_adl_3394512
- 현재 기본값 release_20190318_adl_5832669
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>U-SQL 런타임 버전 문제 해결

발생할 수 있는 런타임 버전에는 두 가지 문제가 있을 수 있습니다.

1. 스크립트나 일부 사용자 코드는 릴리스 간에 동작을 변경 합니다. 이러한 주요 변경 내용은 릴리스 정보 게시와 함께 일반적으로 미리 전달 됩니다. 이러한 주요 변경 사항이 발생 하는 경우 Microsoft 지원에 문의 하 여이 주요 동작 (아직 문서화 되지 않은 경우)을 보고 하 고 이전 런타임 버전에 대해 작업을 제출 하세요.

2. 기본이 아닌 런타임을 사용 하 여 계정에 고정 된 경우 명시적으로 또는 암시적으로 사용 하 고, 런타임이 일정 시간 후에 제거 되었습니다. 누락 된 런타임이 발생 한 경우 현재 기본 런타임으로 실행 되도록 스크립트를 업그레이드 하세요. 추가 시간이 필요한 경우에 문의 하세요 Microsoft 지원

## <a name="see-also"></a>참고 항목

- [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
- [Azure Portal를 사용 하 여 Azure Data Lake Analytics에서 작업 모니터링](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
