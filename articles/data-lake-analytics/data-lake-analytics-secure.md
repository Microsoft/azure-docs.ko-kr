---
title: 여러 사용자에 대한 Azure Data Lake Analytics 보호
description: Azure Data Lake Analytics에서 작업을 실행하도록 여러 사용자를 구성하는 방법을 알아봅니다.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813376"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics에서 작업 정보에 대한 사용자 액세스 구성 

Azure Data Lake Analytics에서 작업을 실행하려면 여러 사용자 계정 또는 서비스 사용자를 사용할 수 있습니다. 

자세한 작업 정보를 보려면 동일한 해당 사용자의 경우 사용자는 작업 폴더의 콘텐츠를 읽을 수 있도록 해야 합니다. 작업 폴더는 `/system/` 디렉터리에 있습니다. 

필요한 사용 권한이 구성되어 있지 않으면 사용자에게 `Graph data not available - You don't have permissions to access the graph data.` 오류가 표시될 수 있습니다. 

## <a name="configure-user-access-to-job-information"></a>작업 정보에 대한 사용자 액세스 구성

**사용자 추가 마법사**를 사용하여 폴더에서 ACL을 구성할 수 있습니다. 자세한 내용은 [새 사용자 추가](data-lake-analytics-manage-use-portal.md#add-a-new-user)를 참조하세요.

세밀한 제어가 필요하거나 사용 권한을 스크립팅해야 하는 경우 다음과 같이 폴더를 보호합니다.

1. 루트 폴더에서 액세스 ACL을 통해 **실행** 권한을 부여합니다.
   - /
   
2. 작업 폴더가 들어 있는 폴더에서 액세스 ACL 및 기본 ACL을 통해 **실행** 및 **읽기** 권한을 부여합니다. 예를 들어 2018년 5월 25일에 실행된 특정 작업의 경우 다음과 같은 폴더에 액세스할 수 있어야 합니다.
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>다음 단계
[새 사용자 추가](data-lake-analytics-manage-use-portal.md#add-a-new-user)
