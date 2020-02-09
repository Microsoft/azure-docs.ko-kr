---
title: Azure 방호 진단 로그 사용 및 사용
description: 이 문서에서는 Azure 방호 진단 로그를 사용 하도록 설정 하 고 작업 하는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989453"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>요새 진단 로그 사용 및 사용

사용자가 Azure 방호를 사용 하 여 워크 로드에 연결 하면, 원격 세션의 진단 정보를 로깅할 수 있습니다. 그런 다음 진단을 사용 하 여 어떤 사용자가 어떤 시간에 어떤 작업에 연결 되어 있는지와 관련 된 로깅 정보를 볼 수 있습니다. 진단을 사용 하려면 Azure 방호에서 진단 로그를 사용 하도록 설정 해야 합니다. 이 문서는 진단 로그를 사용 하도록 설정 하 고 로그를 확인 하는 데 도움이 됩니다.

## <a name="enable"></a>진단 로그 사용

1. [Azure Portal](https://portal.azure.com)에서 azure 방호 리소스로 이동 하 고 azure 방호 페이지에서 **진단 설정** 을 선택 합니다.

   ![진단 설정](./media/diagnostic-logs/1diagnostics-settings.png)
2. **진단 설정**을 선택 하 고 **+ 진단 설정 추가** 를 선택 하 여 로그 대상을 추가 합니다.

   ![진단 설정 추가](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. **진단 설정** 페이지에서 진단 로그를 저장 하는 데 사용할 저장소 계정의 유형을 선택 합니다.

   ![저장소 위치 선택](./media/diagnostic-logs/3add-storage-account.png)
4. 설정을 완료 하면 다음 예제와 같이 표시 됩니다.

   ![설정 예](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>진단 로그 보기

진단 로그에 액세스 하려면 진단 설정을 사용 하도록 설정 하는 동안 지정한 저장소 계정을 직접 사용할 수 있습니다.

1. 저장소 계정 리소스, **컨테이너로**차례로 이동 합니다. 저장소 계정 blob 컨테이너에서 만든 **bastionauditlogs** blob이 표시 됩니다.

   ![진단 설정](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 컨테이너 내부로 이동 하면 블로그의 다양 한 폴더가 표시 됩니다. 이러한 폴더는 Azure 방호 리소스의 리소스 계층 구조를 표시 합니다.

   ![진단 설정 추가](./media/diagnostic-logs/2-resource-h.png)
3. 액세스/확인 하려는 진단 로그를 포함 하는 Azure 방호 리소스의 전체 계층으로 이동 합니다. ' Y = ', ' m = ', ' d = ', ' h = ' 및 ' m = '은 진단 로그의 연도, 월, 일, 시 및 분을 각각 표시 합니다.

   ![저장소 위치 선택](./media/diagnostic-logs/3-resource-location.png)
4. Azure 방호에서 만든 json 파일을 찾습니다. 해당 기간에 대한 진단 로그 데이터를 포함 합니다.

5. 저장소 blob 컨테이너에서 json 파일을 다운로드 합니다. Json 파일의 예제 항목은 참조용으로 다음과 같이 표시 됩니다.

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>다음 단계

[요새 FAQ](bastion-faq.md)를 읽어 보세요.
