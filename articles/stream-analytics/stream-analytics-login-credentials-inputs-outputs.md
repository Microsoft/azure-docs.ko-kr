---
title: Azure Stream Analytics 작업에서 로그인 자격 증명 순환
description: 이 문서에서는 Azure Stream Analytics 작업에서 입력 및 출력 싱크의 자격 증명을 업데이트하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 14e24c1e9a61eb7ea73a949e17ffbf8c5b768f05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479665"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Stream Analytics 작업에서 입력 및 출력을 위한 로그인 자격 증명 순환

Stream Analytics 작업의 입력 또는 출력에대 한 자격 증명을 다시 생성할 때마다 새 자격 증명으로 작업을 업데이트해야 합니다. 자격 증명을 업데이트하기 전에 작업을 중지해야 하며, 작업이 실행되는 동안에는 자격 증명을 바꿀 수 없습니다. 작업 중지 및 다시 시작 간의 지연 시간을 줄이기 위해 Stream Analytics는 마지막 출력에서 작업을 다시 시작하도록 지원합니다. 이 항목에서는 로그인 자격 증명을 순환하고 새 자격 증명으로 작업을 다시 시작하는 과정을 설명합니다.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>새 자격 증명을 다시 생성하고 새 자격 증명으로 작업 업데이트 

이 섹션에서는 Blob 스토리지, Event Hubs, SQL Database 및 Table Storage에 대한 자격 증명을 다시 생성하는 과정을 안내합니다. 

### <a name="blob-storagetable-storage"></a>Blob Storage/Table Storage
1. Azure Portal에 로그인하고 Stream Analytics 작업에 대한 입/출력으로 사용한 저장소 계정으로 이동합니다.    
2. 설정 섹션에서 **액세스 키**를 엽니다. 두 개의 기본 키(key1, key2) 중에서 작업에서 사용하지 않은 키를 선택하고 다시 생성합니다.  
   ![저장소 계정에 대한 키 다시 생성](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. 새로 생성한 키를 복사합니다.    
4. Azure Portal에서 Stream Analytics 작업으로 이동한 후 **중지**를 선택하고 작업이 중지될 때까지 기다립니다.    
5. 자격 증명을 업데이트하려는 Blob/Table Storage 입/출력을 찾습니다.    
6. **Storage 계정 키** 필드를 찾아 새로 생성된 키를 붙여 넣고 **저장**을 클릭합니다.    
7. 변경 내용을 저장할 때 연결 테스트가 자동으로 시작되며 알림 탭에서 확인할 수 있습니다. 다음과 같이 업데이트 저장에 해당하는 알림과 연결 테스트에 대한 알림이 하나씩 표시됩니다.  
   ![키 편집 후 알림](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. [마지막으로 중지된 시간부터 작업 시작](#start-your-job-from-the-last-stopped-time) 섹션을 계속 진행합니다.

### <a name="event-hubs"></a>이벤트 허브(영문)

1. Azure Portal에 로그인하고 Stream Analytics 작업에 대한 입/출력으로 사용한 이벤트 허브로 이동합니다.    
2. 설정 섹션에서 **공유 액세스 정책**을 열고 필요한 액세스 정책을 선택합니다. **기본 키** 및 **보조 키** 중에서 작업에서 사용되지 않은 키를 선택하고 다시 생성합니다.  
   ![Event Hubs에 대한 키 다시 생성](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. 새로 생성한 키를 복사합니다.    
4. Azure Portal에서 Stream Analytics 작업으로 이동한 후 **중지**를 선택하고 작업이 중지될 때까지 기다립니다.    
5. 자격 증명을 업데이트하려는 Event Hubs 입/출력을 찾습니다.    
6. **Event Hubs 정책 키** 필드를 찾아 새로 생성된 키를 붙여 넣고 **저장**을 클릭합니다.    
7. 변경 내용을 저장할 때 연결 테스트가 자동으로 시작되며 성공적으로 통과했는지 확인합니다.    
8. [마지막으로 중지된 시간부터 작업 시작](#start-your-job-from-the-last-stopped-time) 섹션을 계속 진행합니다.

### <a name="sql-database"></a>SQL Database

기존 사용자의 로그인 자격 증명을 업데이트하려면 SQL Database에 연결해야 합니다. Azure Portal 또는 SQL Server Management Studio와 같은 클라이언트 쪽 도구를 사용하여 자격 증명을 업데이트할 수 있습니다. 이 섹션에서는 Azure Portal을 사용하여 자격 증명을 업데이트하는 과정을 보여 줍니다.

1. Azure Portal에 로그인하고 Stream Analytics 작업에 대한 출력으로 사용한 SQL Database로 이동합니다.    
2. **데이터 탐색기**에서 데이터베이스에 로그인/연결하고 권한 부여 유형으로 **SQL Server 인증**을 선택한 후 **로그인** 및  **암호** 세부 정보를 입력하고 **확인**을 선택합니다.  
   ![SQL Database에 대한 자격 증명 다시 생성](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. 쿼리 탭에서 다음 쿼리를 실행하여 사용자 중 한 명의 암호를 변경합니다(`<user_name>`을 사용자 이름으로 바꾸고 `<new_password>`를 새 암호로 바꾸어야 함).  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. 새 암호를 기록해 둡니다.    
5. Azure Portal에서 Stream Analytics 작업으로 이동한 후 **중지**를 선택하고 작업이 중지될 때까지 기다립니다.    
6. 자격 증명을 순환하려는 SQL Database 출력을 찾습니다. 암호를 업데이트하고 변경 내용을 저장합니다.    
7. 변경 내용을 저장할 때 연결 테스트가 자동으로 시작되며 성공적으로 통과했는지 확인합니다.    
8. [마지막으로 중지된 시간부터 작업 시작](#start-your-job-from-the-last-stopped-time) 섹션을 계속 진행합니다.

### <a name="power-bi"></a>Power BI
1. Azure Portal에 로그인하고 Stream Analytics 작업으로 이동한 후 **중지**를 선택하고 작업이 중지될 때까지 기다립니다.    
2. 자격 증명을 갱신하려는 Power BI 출력을 찾은 후 **권한 부여 갱신**을 클릭하고(성공 메시지가 표시됨) 변경 내용을 **저장**합니다.    
3. 변경 내용을 저장할 때 연결 테스트가 자동으로 시작되며 성공적으로 통과했는지 확인합니다.    
4. [마지막으로 중지된 시간부터 작업 시작](#start-your-job-from-the-last-stopped-time) 섹션을 계속 진행합니다.

## <a name="start-your-job-from-the-last-stopped-time"></a>마지막으로 중지된 시간부터 작업 시작

1. 작업의 **개요** 창으로 이동하고 **시작**을 선택하여 작업을 시작합니다.    
2. **마지막으로 중지된 시간**을 선택하고 **시작**을 클릭합니다. 이전에 작업을 실행했으며 일부 출력이 생성된 경우에만 "마지막으로 중지된 시간" 옵션이 표시됩니다. 작업은 마지막 출력 값의 시간에 따라 다시 시작됩니다.
   ![Stream Analytics 작업 시작](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
