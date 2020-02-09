---
title: 포함 파일
description: 포함 파일
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67182610"
---
#### <a name="process-automation"></a>프로세스 자동화

| 리소스 | 최대 제한 |참고|
| --- | --- |---|
| Azure Automation 계정 마다 30 초 마다 제출할 수 있는 새 작업의 최대 수 (예약 되지 않은 작업) |100 |이 한도에 도달 하면 작업 만들기에 대한 후속 요청이 실패 합니다. 클라이언트는 오류 응답을 받습니다.|
| Automation 계정 (예약 되지 않은 작업) 당 동일한 인스턴스에서 동시에 실행 되는 작업의 최대 수 |200 |이 한도에 도달 하면 작업 만들기에 대한 후속 요청이 실패 합니다. 클라이언트는 오류 응답을 받습니다.|
| 30 일 롤링 기간의 작업 메타 데이터에 대한 최대 저장소 크기 | 10gb (약 400만 개 작업)|이 한도에 도달 하면 작업 만들기에 대한 후속 요청이 실패 합니다. |
| 최대 작업 스트림 제한|1MB|단일 스트림은 1mb 보다 클 수 없습니다.|
| Automation 계정 마다 30 초 마다 가져올 수 있는 모듈의 최대 수 |5 ||
| 모듈의 최대 크기 |100 MB ||
| 작업 실행 시간, 무료 계층 |월별 구독당 500분 ||
| Sandbox 당 허용 되는 최대 디스크 공간<sup>1</sup> |1GB |Azure 샌드박스에만 적용 됩니다.|
| 샌드박스에 지정 된 최대 메모리 양<sup>1</sup> |400 MB |Azure 샌드박스에만 적용 됩니다.|
| Sandbox 당 허용 되는 최대 네트워크 소켓 수<sup>1</sup> |1,000 |Azure 샌드박스에만 적용 됩니다.|
| Runbook 당 허용 되는 최대 런타임<sup>1</sup> |3시간 |Azure 샌드박스에만 적용 됩니다.|
| 구독의 최대 Automation 계정 수 |제한 없음 ||
| Automation 계정 당 최대 Hybrid Worker 그룹 수|4,000||
|단일 Hybrid Runbook Worker에서 실행할 수 있는 최대 동시 작업 수|50 ||
| 최대 runbook 작업 매개 변수 크기   | 512 킬로 비트||
| 최대 runbook 매개 변수   | 50|50-매개 변수 제한에 도달 하면 JSON 또는 XML 문자열을 매개 변수에 전달 하 고 runbook을 사용 하 여 구문 분석할 수 있습니다.|
| 최대 webhook 페이로드 크기 |  512 킬로 비트|
| 작업 데이터가 유지 되는 최대 일 수|30일|
| 최대 PowerShell 워크플로 상태 크기 |5MB| 워크플로의 검사점을 할 때 PowerShell 워크플로 runbook에 적용 됩니다.|

<sup>1</sup> 샌드박스는 여러 작업에서 사용할 수 있는 공유 환경입니다. 동일한 샌드박스를 사용 하는 작업은 샌드박스에 대한 리소스 제한에 의해 바인딩됩니다.

#### <a name="change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리

다음 표에서는 변경 내용 추적에 대한 컴퓨터별 추적 된 항목 제한을 보여 줍니다.

| **Resource** | **제한**| **참고** |
|---|---|---|
|파일|500||
|레지스트리|250||
|Windows 소프트웨어|250|에는 소프트웨어 업데이트가 포함 되어 있지 않습니다.|
|Linux 패키지|1250||
|서비스|250||
|디먼|250||

#### <a name="update-management"></a>업데이트 관리

다음 표에서는 업데이트 관리에 대한 제한을 보여 줍니다.

| **Resource** | **제한**| **참고** |
|---|---|---|
|업데이트 배포 당 컴퓨터 수|1000||