---
title: ODBC를 사용 하 여 Azure 데이터 탐색기에 연결
description: 이 문서에서는 Azure 데이터 탐색기에 대한 ODBC (Open Database Connectivity) 연결을 설정 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034026"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC를 사용 하 여 Azure 데이터 탐색기에 연결

[ODBC](/sql/odbc/reference/odbc-overview)(Open database Connectivity)는 데이터베이스 액세스를 위해 널리 사용 되는 API (응용 프로그래밍 인터페이스)입니다. 전용 커넥터가 없는 응용 프로그램에서 ODBC를 사용 하 여 Azure 데이터 탐색기에 연결 합니다.

백그라운드에서 응용 프로그램은 ODBC 인터페이스의 함수를 호출 합니다 .이 함수는 *드라이버*라는 데이터베이스 전용 모듈에서 구현 됩니다. Azure 데이터 탐색기는[MS TDS](/azure/kusto/api/tds/)(SQL Server 통신 프로토콜)의 하위 집합을 지원 하므로 ODBC driver for SQL Server를 사용할 수 있습니다.

다음 비디오를 사용 하 여 ODBC 연결을 만드는 방법을 배울 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

또는 아래에 설명 [된 대로 ODBC 데이터 원본을 구성할](#configure-the-odbc-data-source) 수 있습니다. 

이 문서에서는 odbc를 지 원하는 모든 응용 프로그램에서 Azure 데이터 탐색기에 연결할 수 있도록 SQL Server ODBC 드라이버를 사용 하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>선행 조건

다음이 필요합니다.

* 운영 체제에 [17.2.0.1 이상 버전을 Microsoft ODBC Driver for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server) 합니다.

## <a name="configure-the-odbc-data-source"></a>ODBC 데이터 원본 구성

SQL Server 용 ODBC 드라이버를 사용 하 여 ODBC 데이터 원본을 구성 하려면 다음 단계를 따르세요.

1. Windows에서 *Odbc 데이터 원본을*검색 하 고 Odbc 데이터 원본 데스크톱 앱을 엽니다.

1. **추가**를 선택합니다.

    ![데이터 원본 추가](media/connect-odbc/add-data-source.png)

1. **SQL Server에 대해 ODBC Driver 17을** 선택 하 고 **마침**을 선택 합니다.

    ![드라이버 선택](media/connect-odbc/select-driver.png)

1. 연결할 연결 및 클러스터의 이름과 설명을 입력 하 고 **다음**을 선택 합니다. 클러스터 URL은 ClusterName\>\<형식 이어야 합니다. *\<Region\>. kusto.windows.net*.

    ![서버 선택](media/connect-odbc/select-server.png)

1. **Active Directory 통합** 을 선택한 후 **다음**을 선택 합니다.

    ![Active Directory 통합](media/connect-odbc/active-directory-integrated.png)

1. 예제 데이터가 포함 된 데이터베이스를 선택 하 **고 다음을 선택 합니다.**

    ![기본 데이터베이스 변경](media/connect-odbc/change-default-database.png)

1. 다음 화면에서 모든 옵션을 기본값으로 유지 하 고 **마침**을 선택 합니다.

1. **테스트 데이터 원본**을 선택 합니다.

    ![테스트 데이터 소스](media/connect-odbc/test-data-source.png)

1. 테스트에 성공 했는지 확인 한 다음 **확인**을 선택 합니다. 테스트에 성공 하지 못한 경우 이전 단계에서 지정한 값을 확인 하 고 클러스터에 연결할 수 있는 충분 한 권한이 있는지 확인 합니다.

    ![테스트 성공](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>다음 단계

* [Tableau에서 Azure 데이터 탐색기에 연결](tableau.md)