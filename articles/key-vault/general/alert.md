---
title: Azure Key Vault 모니터링 및 경고 | Microsoft Docs
description: 대시보드를 만들어 주요 자격 증명 모음의 상태를 모니터링 하 고 경고를 구성 합니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 6b179dd2662bf6fa545ea44b723671f1499b9e35
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589013"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure Key Vault에 대 한 모니터링 및 경고

## <a name="overview"></a>개요

주요 자격 증명 모음을 사용 하 여 프로덕션 비밀을 저장 하기 시작한 후에는 주요 자격 증명 모음의 상태를 모니터링 하 여 서비스가 의도 한 대로 작동 하는지 확인 하는 것이 중요 합니다. 서비스의 크기를 조정하기 시작하면 키 자격 증명 모음으로 전송되는 요청 수가 증가합니다. 이로 인해 요청 대기 시간이 길어질 수 있으며 극단적인 경우에는 서비스의 성능에 영향을 줄 수 있는 요청이 정체됩니다. 키 자격 증명 모음이 비정상적인 수의 오류 코드를 전송 하는 경우에도 경고 해야 하므로 액세스 정책 또는 방화벽 구성 문제를 신속 하 게 알릴 수 있습니다. 이 문서에서는 다음 항목을 다룹니다.

+ 모니터링할 기본 Key Vault 메트릭
+ 메트릭을 구성 하 고 대시보드를 만드는 방법 
+ 지정 된 임계값에 대 한 경고를 만드는 방법 

## <a name="basic-key-vault-metrics-to-monitor"></a>모니터링할 기본 Key Vault 메트릭

+ 자격 증명 모음 가용성  
+ 자격 증명 모음 채도 
+ 서비스 API 대기 시간 
+ Total Service API 적중 (활동 유형별 필터링) 
+ 오류 코드 (상태 코드를 기준으로 필터링) 

**자격 증명 모음 가용성** -이 메트릭은 항상 100% 여야 합니다 .이 메트릭은 주요 자격 증명 모음에 가동 중단이 발생 한 경우를 신속 하 게 표시할 수 있으므로 모니터링에 중요 한 메트릭입니다. 

**자격 증명 모음 채도** – 키 자격 증명 모음이 수행할 수 있는 초당 요청 수는 수행 중인 작업 유형에 따라 달라 집니다. 일부 자격 증명 모음 작업에는 초당 낮은 요청 임계값이 있습니다. 이 메트릭은 모든 작업 유형에 서 key vault의 총 사용량을 집계 하 여 현재 주요 자격 증명 모음 사용을 나타내는 백분율 값을 포함 합니다. 주요 자격 증명 모음 서비스 제한의 전체 목록은 다음 문서를 참조 하세요. [Azure Key Vault 서비스 제한](service-limits.md)

**서비스 API 대기 시간** -이 메트릭은 key vault에 대 한 호출의 평균 대기 시간을 보여 줍니다. 키 자격 증명 모음이 서비스 제한 내에 있을 수 있지만 주요 자격 증명 모음 사용률이 높으면 응용 프로그램의 다운스트림에 오류가 발생 하는 대기 시간이 발생할 수 있습니다. 

**총 API 적중 횟수** -이 메트릭은 키 자격 증명 모음에 대 한 모든 호출을 보여 줍니다. 그러면 주요 자격 증명 모음을 호출 하는 응용 프로그램을 식별 하는 데 도움이 됩니다. 

**오류 코드** –이 메트릭은 주요 자격 증명 모음에 비정상적인 오류가 발생 한 경우를 표시 합니다. 오류 코드 및 문제 해결 지침의 전체 목록은 다음 문서를 참조 하세요. [Azure Key Vault REST API 오류 코드](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>메트릭을 구성 하 고 대시보드를 만드는 방법

1. Azure Portal에 로그인합니다.
2. Key Vault로 이동 합니다.
3. **모니터링** 아래에서 **메트릭** 선택 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-1.png)

4. 대시보드에 표시 하려는 차트의 제목을 업데이트 합니다. 
5. 범위를 선택 합니다. 이 예제에서는 단일 키 자격 증명 모음을 선택 합니다. 
6. **전체 자격 증명 모음 가용성** 및 집계 **평균** 메트릭을 선택 합니다. 
7. 시간 범위를 지난 24 시간으로 업데이트 하 고 시간 세분성을 1 분으로 업데이트 합니다. 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-2.png)

8. 자격 증명 모음 채도 및 서비스 API 대기 시간 메트릭에 대해 위의 단계를 반복 합니다. 대시보드에 **고정** 을 선택 하 여 메트릭을 대시보드에 저장 합니다. 

> [!IMPORTANT]
> "대시보드에 고정"을 선택 하 고 구성 하는 모든 메트릭을 저장 합니다. 페이지를 그대로 두고 저장 하지 않고 돌아오면 구성 변경 내용이 손실 됩니다. 

9. 주요 자격 증명 모음에 대 한 모든 유형의 작업을 모니터링 하려면 **Total SERVICE API 적중** 메트릭을 사용 하 고 **활동 유형별 분할 적용** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-3.png)

10. 주요 자격 증명 모음에 대 한 오류 코드를 모니터링 하려면 **Total SERVICE API 결과** 메트릭을 사용 하 고 **활동 유형별 분할 적용** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-4.png)

이제 다음과 같은 대시보드가 표시 됩니다. 각 타일의 오른쪽 위에 있는 3 개의 점을 클릭 하 고 필요에 따라 타일을 다시 정렬 하 고 크기를 조정할 수 있습니다. 

대시보드를 저장 하 고 게시 한 후에는 Azure 구독에서 새 리소스를 만듭니다. 언제 든 지 "공유 대시보드"를 검색 하 여 볼 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Key Vault에 대 한 경고를 구성 하는 방법 

이 섹션에서는 주요 자격 증명 모음이 비정상 상태인 경우 팀에 즉시 조치를 취할 수 있도록 주요 자격 증명 모음에 대 한 경고를 구성 하는 방법을 보여 줍니다. 전자 메일을 보내거나, 팀 DL로 보내거나, event grid 알림을 발생 시키거나, 전화 번호를 호출 하거나 텍스트를 전송 하는 경고를 구성할 수 있습니다. 또한 고정 된 값을 기반으로 하는 정적 경고를 선택 하거나, 모니터링 되는 메트릭이 정의 된 시간 범위 내에서 특정 횟수의 key vault 평균 제한을 초과 하는 경우 경고 하는 동적 경고를 선택할 수 있습니다. 

> [!IMPORTANT]
> 새로 구성 된 경고가 알림 전송을 시작 하는 데 최대 10 분이 걸릴 수 있습니다. 

### <a name="configure-an-action-group"></a>작업 그룹 구성 

작업 그룹은 알림 및 속성의 구성 가능한 목록입니다.

1. Azure Portal에 로그인합니다.
2. 검색 상자에서 **경고** 검색
3. **작업 관리** 선택

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-6.png)

4. **+ 작업 그룹 추가** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-7.png)

5. 작업 그룹에 대 한 **작업 유형을** 선택 합니다. 이 예제에서는 전자 메일 알림을 만듭니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-9.png)

6. 페이지 아래쪽에서 **확인**을 클릭합니다. 작업 그룹을 성공적으로 만들었습니다. 

이제 작업 그룹을 구성 했으므로 주요 자격 증명 모음 경고 임계값을 구성 합니다. 

### <a name="configure-alert-thresholds"></a>경고 임계값 구성 

1. Azure Portal에서 주요 자격 증명 모음 리소스를 선택 하 고 **모니터링** 아래에서 **경고** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-10.png)

2. **새 경고 규칙** 선택

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-11.png)

3. 경고 규칙의 범위를 선택 합니다. 단일 자격 증명 모음 또는 여러 개를 선택할 수 있습니다. 

> [!IMPORTANT]
> 경고 범위에 대해 여러 자격 증명 모음을 선택 하는 경우 선택한 모든 자격 증명 모음이 동일한 지역에 있어야 합니다. 다른 지역의 자격 증명 모음에 대해 별도의 경고 규칙을 구성 해야 합니다. 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-12.png)

4. 경고에 대 한 조건을 선택 합니다. 다음 신호 중 하나를 선택 하 고 경고에 대 한 논리를 정의할 수 있습니다. Key Vault 팀에서는 다음과 같은 경고 임계값을 구성 하는 것이 좋습니다. 

    + Key Vault 가용성은 100% 아래로 떨어집니다 (정적 임계값).
    + Key Vault 대기 시간이 500ms (정적 임계값) 보다 큽니다. 
    + 전체 자격 증명 모음 채도가 75% (정적 임계값) 보다 큽니다. 
    + 전체 자격 증명 모음 채도가 평균을 초과 합니다 (동적 임계값).
    + 평균 보다 높은 총 오류 코드 (동적 임계값) 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>예 1: 대기 시간에 대 한 정적 경고 임계값 구성

**전체 서비스 API 대기 시간** 을 신호 이름으로 선택
> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-14.png)

다음 구성 매개 변수를 참조 하세요.

+ 임계값을 **고정** 으로 설정 합니다. 
+ 연산자를 다음 **보다 큼** 으로 설정 합니다.
+ 집계 유형을 **평균** 으로 설정
+ 임계값을 **500** 으로 설정 합니다.
+ 집계 기간을 **5 분** 으로 설정
+ 평가 빈도를 **1 분** 으로 설정 합니다.
+ **완료**를 선택합니다.  

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>예 2: 자격 증명 모음 채도에 대 한 동적 경고 임계값 구성 

동적 경고를 사용 하는 경우 선택한 key vault의 기록 데이터를 볼 수 있습니다. 파란색 영역은 key vault의 평균 사용량을 나타냅니다. 빨간색 영역에는 경고 구성의 다른 기준이 충족 되는 경우 경고를 트리거한 스파이크가 표시 됩니다. 빨간색 점은 집계 기간 동안 경고에 대 한 조건이 충족 된 위반 인스턴스를 표시 합니다. 설정 된 시간 내에 특정 위반 횟수 후에 발생 하도록 경고를 설정할 수 있습니다. 이전 데이터를 포함 하지 않으려는 경우 고급 설정에서 이전 데이터를 제외 하는 옵션이 있습니다. 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-16.png)

다음 구성 매개 변수를 참조 하세요.

+ 임계값을 **동적** 으로 설정 
+ 연산자를 다음 **보다 큼** 으로 설정 합니다.
+ 집계 유형을 **평균** 으로 설정
+ 임계값 민감도를 **보통** 으로 설정
+ 집계 기간을 **5 분** 으로 설정
+ 평가 빈도를 **1 분** 으로 설정 합니다.
+ **선택 사항** 고급 설정 구성 
+ **완료**를 선택합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-17.png)

5. 구성한 작업 그룹을 추가 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-18.png)

6. 경고를 사용 하도록 설정 하 고 심각도 할당

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-19.png)

7. 경고 만들기 


## <a name="next-steps"></a>다음 단계

축 하 합니다. 이제 모니터링 대시보드를 만들고 key vault에 대 한 경고를 구성 했습니다. 위의 모든 단계를 수행한 후에는 key vault가 구성 된 경고 조건을 충족 하는 경우 전자 메일 경고를 받게 됩니다. 아래에 예제가 나와 있습니다. 이 문서에서 설정한 도구를 사용 하 여 주요 자격 증명 모음의 상태를 적극적으로 모니터링 합니다. 

### <a name="example-email-alert"></a>예제 전자 메일 경고 

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-20.png)
