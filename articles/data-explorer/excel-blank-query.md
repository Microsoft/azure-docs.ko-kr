---
title: Microsoft Excel로 가져온 Azure 데이터 탐색기 Kusto 쿼리를 사용 하 여 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기 Kusto 쿼리를 Microsoft Excel로 가져오는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849091"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Microsoft Excel로 가져온 Azure 데이터 탐색기 Kusto 쿼리를 사용 하 여 데이터 시각화

Azure 데이터 탐색기는 Excel에서 데이터에 연결 하는 두 가지 옵션을 제공 합니다. 기본 커넥터를 사용 하거나 Azure 데이터 탐색기에서 쿼리를 가져옵니다. 이 문서에서는 Azure 데이터 탐색기에서 Excel로 쿼리를 가져와서 데이터를 시각화 하는 방법을 보여 줍니다. 데이터에 대한 추가 계산 또는 시각화를 수행 하려면 Kusto query를 Excel 데이터 원본으로 추가 합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure [데이터 탐색기 도움말 클러스터](https://dataexplorer.azure.com/clusters/help/databases/Samples) 에 연결할 수 있도록 azure Active directory의 구성원 인 조직 전자 메일 계정 
<br>or</br>
* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md) 를 만들고 [AZURE 데이터 탐색기 웹 UI 응용 프로그램](https://dataexplorer.azure.com/)에 로그인 합니다.

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto query를 Excel 데이터 원본으로 정의

1. [Azure 데이터 탐색기 웹 UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)에서 쿼리를 실행 하 고 결과를 확인 합니다.

1. **공유** 탭을 선택 하 고 **Power BI 쿼리를**선택 합니다.

    ![Power BI에 대한 웹 UI 쿼리](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. 다음 알림과 함께 창이 표시 됩니다.

    ![클립보드로 쿼리 내보내기](media/excel-blank-query/query-exported-to-clipboard.png)

1. **Microsoft Excel**을 엽니다.

1. **데이터** 탭에서 **기타 원본에서** **데이터 가져오기** >  > **빈 쿼리**를 선택 합니다.

    ![데이터 가져오기 및 빈 쿼리 선택](media/excel-blank-query/get-data-blank-query.png)

1. **파워 쿼리 편집기** 창이 열립니다. 창에서 **고급 편집기**을 선택 합니다.

    ![파워 쿼리 편집기 창](media/excel-blank-query/power-query-editor.png)

1. **고급 편집기** 창에서 내보낸 쿼리를 클립보드로 붙여넣고 **완료**를 선택 합니다.

    ![고급 편집기 쿼리](media/excel-blank-query/advanced-editor-query.png)    

1. 인증 하려면 **자격 증명 편집**을 선택 합니다.

    ![자격 증명 편집](media/excel-blank-query/edit-credentials.png)

1. **조직 계정** 을 선택 하 고 **로그인**합니다. 로그인 프로세스를 완료 한 다음 **연결**을 선택 합니다.

    ![로그인 완료](media/excel-blank-query/complete-sign-in.png)

    이전 단계를 반복 하 여 쿼리를 더 추가 합니다. 쿼리의 이름을 더 의미 있는 이름으로 바꿀 수 있습니다.

1. **& 로드 닫기** 단추를 선택 하 여 데이터를 Excel로 가져옵니다.

    ![닫기 및 로드를 선택 합니다.](media/excel-blank-query/close-and-load.png)

1. 이제 데이터가 Excel에 있습니다. **새로 고침** 단추를 선택 하 여 쿼리를 새로 고칩니다.

    ![Excel에서 데이터 보기](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>다음 단계

[Excel 용 Azure 데이터 탐색기 커넥터를 사용 하 여 데이터 시각화](excel-connector.md)