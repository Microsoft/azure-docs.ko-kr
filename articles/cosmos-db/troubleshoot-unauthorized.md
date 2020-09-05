---
title: 권한 없는 예외 Azure Cosmos DB 문제 해결
description: 권한 없는 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a4f51b641ca38b2b6f74bb77928537270d12f1e8
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870853"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>권한 없는 예외 Azure Cosmos DB 진단 및 문제 해결

HTTP 401: HTTP 요청에서 찾은 MAC 서명이 계산 된 시그니처와 동일 하지 않습니다.
"HTTP 요청에서 찾은 MAC 서명이 계산 된 시그니처와 동일 하지 않습니다." 라는 오류 메시지를 401 받은 경우 다음과 같은 시나리오에서 발생할 수 있습니다.

이전 Sdk의 경우 예외는 올바른 401 권한 없는 예외 대신 잘못 된 JSON 예외로 표시 될 수 있습니다. 최신 Sdk는이 시나리오를 올바르게 처리 하 고 올바른 오류 메시지를 제공 합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 인증 되지 않은 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>가장 일반적인 시나리오는 키가 제대로 회전 되지 않은 것입니다.
401 MAC 시그니처는 키 회전 직후에 표시 되며 결국에는 변경 없이 중지 됩니다. 

#### <a name="solution"></a>해결 방법:
키가 회전 되었으며 [모범 사례](secure-access-to-data.md#key-rotation)를 따르지 않았습니다. Azure Cosmos DB 계정 키 회전은 Azure Cosmos DB 계정 크기에 따라 몇 초에서 며칠까지 걸릴 수 있습니다.

### <a name="the-key-is-misconfigured"></a>키가 잘못 구성 되었습니다. 
401 MAC 서명 문제가 일관 되 고 해당 키를 사용 하는 모든 호출에 대해 발생 합니다.

#### <a name="solution"></a>해결 방법:
응용 프로그램에서 키가 잘못 구성 되었으며 계정에 대해 잘못 된 키를 사용 하 고 있거나 전체 키가 복사 되지 않았습니다.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>응용 프로그램에서 쓰기 작업에 대 한 읽기 전용 키를 사용 하 고 있습니다.
401 MAC 서명 문제는 만들기 또는 바꾸기와 같은 쓰기 작업에 대해서만 발생 하지만 읽기 요청이 성공 합니다.

#### <a name="solution"></a>해결 방법:
응용 프로그램에서 읽기/쓰기 키를 사용 하도록 전환 하 여 작업을 성공적으로 완료할 수 있도록 합니다.

### <a name="race-condition-with-create-container"></a>컨테이너 만들기를 사용 하 여 경합 상태
401 MAC 서명 문제는 컨테이너를 만든 직후에 표시 됩니다. 이 문제는 컨테이너 만들기가 완료 될 때 까지만 발생 합니다.

#### <a name="solution"></a>해결 방법:
컨테이너를 만드는 경합 조건이 있습니다. 컨테이너 만들기가 완료 되기 전에 응용 프로그램 인스턴스가 컨테이너에 액세스 하려고 합니다. 이 경합 상태에 대 한 가장 일반적인 시나리오는 응용 프로그램이 실행 중이 고 컨테이너를 삭제 한 다음 같은 이름으로 다시 만드는 경우입니다. SDK는 새 컨테이너를 사용 하려고 하지만 컨테이너 만들기는 아직 진행 중 이므로 키를 포함 하지 않습니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-dot-net-sdk.md) 합니다.
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.