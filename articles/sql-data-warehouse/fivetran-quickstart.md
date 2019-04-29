---
title: Azure SQL Data Warehouse에 대한 Fivetran 빠른 시작 | Microsoft Docs
description: Fivetran 및 Azure SQL Data Warehouse로 빨리 시작합니다.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 1d8b6d2df21fe0aabe85754e061db4deb129b32e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082514"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Fivetran 및 SQL Data Warehouse로 빠른 시작

이 빠른 시작에서는 Azure SQL Data Warehouse를 사용하도록 새 Fivetran 사용자를 설정하는 방법을 설명합니다. 이 문서에서는 기존 SQL Data Warehouse 인스턴스가 있다고 가정합니다.

## <a name="set-up-a-connection"></a>연결 설정

1. SQL Data Warehouse에 연결하는 데 사용하는 정규화된 서버 이름 및 데이터베이스 이름을 찾습니다.
    
    이 정보를 찾는 데 도움이 필요한 경우 [Azure SQL Data Warehouse에 연결](sql-data-warehouse-connect-overview.md)을 참조하세요.

2. 설정 마법사에서 데이터베이스를 직접 연결할지 또는 SSH 터널을 통해 연결할지 선택합니다.

   데이터베이스에 직접 연결을 선택하는 경우 액세스를 허용하는 방화벽 규칙을 만들어야 합니다. 이 방법이 가장 간단하고 가장 안전한 방법입니다.

   SSH 터널을 통한 연결을 선택하면 Fivetran이 네트워크의 별도 서버에 연결합니다. 해당 서버에서 데이터베이스에 대한 SSH 터널을 제공합니다. 데이터베이스가 가상 네트워크에서 액세스할 수 없는 서브넷에 있는 경우 이 방법을 사용해야 합니다.

3. Fivetran에서 SQL Data Warehouse 인스턴스에 들어오는 연결을 허용하도록 서버 수준 방화벽에 IP 주소 **52.0.2.4**를 추가합니다.

   자세한 내용은 [서버 수준 방화벽 규칙 만들기](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)를 참조하세요.

## <a name="set-up-user-credentials"></a>사용자 자격 증명 설정

1. SQL Server Management Studio 또는 원하는 도구를 사용하여 Azure SQL Data Warehouse에 연결합니다. 서버 관리 사용자로 로그인합니다. 그런 후에, 다음 SQL 명령을 실행하여 Fivetran의 사용자를 만듭니다.
    - master 데이터베이스의 경우: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - SQL Data Warehouse 데이터베이스의 경우:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Fivetran 사용자에게 웨어하우스에 대한 다음 사용 권한을 부여합니다.

    ```
    GRANT CONTROL to fivetran;
    ```

    사용자가 PolyBase를 사용하여 Azure Blob Storage에서 파일을 로드할 때 사용되는 데이터베이스 범위 자격 증명을 만들려면 CONTROL 권한이 필요합니다.

3. Fivetran 사용자에게 적합한 리소스 클래스를 추가합니다. 사용하는 리소스 클래스는 columnstore 인덱스를 만드는 데 필요한 메모리에 따라 다릅니다. 예를 들어 Marketo, Salesforce 등의 제품과 통합하려면 해당 제품이 사용하는 대량 데이터와 다수의 열 때문에 상위 리소스 클래스가 필요합니다. 상위 리소스 클래스에서 columnstore 인덱스를 만들려면 더 많은 메모리가 필요합니다.

    정적 리소스 클래스를 사용하는 것이 좋습니다. `staticrc20` 리소스 클래스로 시작할 수 있습니다. `staticrc20` 리소스 클래스는 사용하는 성능 수준에 관계없이 각 사용자에 대해 200MB를 할당합니다. columnstore 인덱싱이 초기 리소스 클래스 수준에서 실패하면 리소스 클래스를 늘립니다.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    자세한 내용은 [메모리 및 동시성 한도](memory-and-concurrency-limits.md) 및 [리소스 클래스](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)를 참조하세요.


## <a name="sign-in-to-fivetran"></a>Fivetran에 로그인

Fivetran에 로그인하려면 SQL Data Warehouse에 액세스하는 데 사용하는 자격 증명을 입력합니다. 

* 호스트(서버 이름).
* 포트.
* 데이터베이스.
* 사용자 (사용자 이름은 반드시 **fivetran\@_server_name_**  여기서 *server_name* Azure 호스트 URI의 일부인: ***server_name * 합니다. database.windows.net**).
* Password.
