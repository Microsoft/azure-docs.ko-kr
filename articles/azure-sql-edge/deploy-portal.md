---
title: Azure Portal을 사용하여 Azure SQL Edge(미리 보기) 배포
description: Azure Portal을 사용하여 Azure SQL Edge(미리 보기)를 배포하는 방법 알아보기
keywords: SQL Edge 배포
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816867"
---
# <a name="deploy-azure-sql-edge-preview"></a>Azure SQL Edge(미리 보기) 배포 

Azure SQL Edge(미리 보기)는 IoT 및 Azure IoT Edge 배포에 최적화된 관계형 데이터베이스 엔진으로, IoT 애플리케이션과 솔루션을 위한 고성능 데이터 스토리지 및 처리 계층을 만드는 기능을 제공합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure IoT Edge를 통해 Azure SQL Edge 모듈 만들기를 시작하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md)를 만듭니다.
* [Azure Portal에서 IoT Edge 디바이스](../iot-edge/how-to-register-device-portal.md)를 등록합니다.
* [Azure Portal에서 IoT Edge 모듈을 배포](../iot-edge/how-to-deploy-modules-portal.md)할 수 있게 IoT Edge 디바이스를 준비합니다.

> [!NOTE]
> Azure Linux VM을 IoT Edge 디바이스로 배포하려면 이 [빠른 시작 가이드](../iot-edge/quickstart-linux.md)를 참조하세요.

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Azure Marketplace의 SQL Edge 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure SQL Edge는 Marketplace를 통해 에지 디바이스에 배포할 수 있습니다.

1. Azure Marketplace에서 Azure SQL Edge 모듈을 찾습니다.<br><br>

   ![MarketPlace의 SQL Edge](media/deploy-portal/find-offer-marketplace.png)

2. 요구 사항에 가장 부합하는 소프트웨어 요금제를 선택하고 **만들기**를 클릭합니다. <br><br>

   ![올바른 소프트웨어 요금제 선택](media/deploy-portal/pick-correct-plan.png)

3. IoT Edge 모듈의 대상 디바이스 페이지에서 다음 세부 정보를 지정한 후 **만들기**를 클릭합니다.

   |**필드**  |**설명**  |
   |---------|---------|
   |Subscription  |  이 IoT Hub가 만들어진 Azure 구독 |
   |IoT Hub   |  IoT Edge 디바이스가 등록된 IoT Hub의 이름이며 "디바이스에 배포" 옵션 선택|
   |IoT Edge 디바이스 이름  |  SQL Edge를 배포할 IoT Edge 디바이스의 이름 |

4. **모듈 설정** 페이지에서 배포 모듈 섹션으로 이동하고 SQL Edge 모듈에 대해 **구성**을 클릭합니다. 

5. **IoT Edge 사용자 지정 모듈** 창에서 원하는 환경 변수 값을 지정하거나 만들기 옵션 및 원하는 모듈 속성을 사용자 지정합니다. 지원되는 환경 변수의 전체 목록은 [SQL Server 컨테이너 환경 변수](/sql/linux/sql-server-linux-configure-environment-variables/)를 참조하세요.

   |**매개 변수**  |**설명**|
   |---------|---------|
   | Name | 모듈의 이름입니다. |
   |SA_PASSWORD  | SQL Edge 관리자 계정의 강력한 암호를 지정합니다. |
   |MSSQL_LCID   | SQL Server에 사용할 언어 ID를 설정합니다. 예를 들어 1036은 프랑스어입니다. |
   |MSSQL_COLLATION | SQL Server에 대한 기본 데이터 정렬을 설정합니다. 이 설정은 데이터 정렬에 대한 언어 ID(LCID)의 기본 매핑을 재정의합니다. |

   > [!NOTE]
   > 모듈의 **이미지 URI** 또는 **ACCEPT_EULA** 설정을 변경하거나 업데이트하지 마세요.

6. **IoT Edge 사용자 지정 모듈** 창에서 컨테이너 만들기 옵션의 원하는 **호스트 포트** 값을 업데이트합니다. 둘 이상의 SQL DB Edge 모듈을 배포해야 하는 경우에는 탑재 옵션을 업데이트하여 영구적 볼륨에 대한 새 원본 & 대상 쌍을 만들어야 합니다. 탑재 및 볼륨에 대한 자세한 내용은 docker 설명서의 [볼륨 사용](https://docs.docker.com/storage/volumes/)을 참조하세요. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. **IoT Edge 사용자 지정 모듈** 창에서 SQL 패키지의 위치와 스트림 분석 작업 정보를 포함하도록 *모듈 쌍의 원하는 속성 설정*을 업데이트합니다. 이러한 두 필드는 선택 사항이며, 데이터베이스 및 스트리밍 작업을 사용하여 SQL Edge 모듈을 배포하려는 경우에 사용해야 합니다.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. **IoT Edge 사용자 지정 모듈** 창에서 *다시 시작 정책*을 항상으로 설정하고 *원하는 상태*를 실행 중으로 설정합니다.
9. **IoT Edge 사용자 지정 모듈** 창에서 **저장**을 클릭합니다.
10. **모듈 설정** 페이지에서 **다음**을 선택합니다.
11. **모듈 설정** 페이지의 **경로 지정(선택 사항)** 에서 모듈 간 통신 또는 모듈과 IoT Edge 허브의 통신 경로를 지정하고 [IoT Edge에 모듈을 배포하고 경로 설정](../iot-edge/module-composition.md)을 참조하세요.
12. **다음**을 클릭합니다.
13. **제출**을 클릭합니다.

## <a name="connect-to-azure-sql-edge"></a>Azure SQL Edge에 연결

다음 단계는 컨테이너 내에서 Azure SQL Edge 명령줄 도구인 **sqlcmd**를 사용 하 여 Azure sql edge에 연결 합니다.

> [!NOTE]
> sqlcmd 도구는 SQL Edge 컨테이너의 ARM64 버전 내에서 사용할 수 없습니다.

1. `docker exec -it` 명령을 사용하여 실행 중인 컨테이너 내에서 대화형 bash 셸을 시작합니다. 다음 예에서는 `azuresqledge` `Name` IoT Edge 모듈의 매개 변수에 지정 된 이름을 지정 합니다.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. 컨테이너 내부로 들어가면 sqlcmd를 사용하여 로컬로 연결합니다. Sqlcmd는 기본적으로 경로에 있지 않으므로 전체 경로를 지정해야 합니다.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > 명령줄에서 암호를 생략하여 입력하라는 메시지가 표시되도록 할 수 있습니다.

3. 성공하면 **sqlcmd** 명령 프롬프트 `1>`이 표시됩니다.

## <a name="create-and-query-data"></a>데이터 만들기 및 쿼리

다음 섹션에서는 **sqlcmd** 및 Transact-SQL을 사용하여 새 데이터베이스를 만들고, 데이터를 추가하고, 간단한 쿼리를 실행하는 단계를 안내합니다.

### <a name="create-a-new-database"></a>새 데이터베이스 만들기

다음 단계에서는 `TestDB`라는 새 데이터베이스를 만듭니다.

1. **sqlcmd** 명령 프롬프트에서 다음 Transact-SQL 명령을 붙여넣어 테스트 데이터베이스를 만듭니다.

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. 다음 줄에 서버에 있는 모든 데이터베이스의 이름을 반환하는 쿼리를 작성합니다.

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>데이터 삽입

다음으로 새 테이블 `Inventory`를 만들고 두 개의 새 행을 삽입합니다.

1. **sqlcmd** 명령 프롬프트에서 컨텍스트를 새 `TestDB` 데이터베이스로 전환합니다.

   ```sql
   USE TestDB
   ```

2. `Inventory`라는 새 테이블을 만듭니다.

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 새 테이블에 데이터를 삽입합니다.

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. `GO`를 입력하여 앞의 명령을 실행합니다.

   ```sql
   GO
   ```

### <a name="select-data"></a>데이터 선택

이제 쿼리를 실행하여 `Inventory` 테이블에서 데이터를 반환합니다.

1. **sqlcmd** 명령 프롬프트에서 `Inventory` 테이블에서 수량이 152보다 큰 행을 반환하는 쿼리를 입력합니다.

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 다음 명령을 실행합니다.

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>sqlcmd 명령 프롬프트 종료

1. **sqlcmd** 세션을 종료하려면 `QUIT`를 입력합니다.

   ```sql
   QUIT
   ```

2. 컨테이너에서 대화형 명령 프롬프트를 종료하려면 `exit`을 입력합니다. 컨테이너는 대화형 bash 셸을 종료한 후에도 계속 실행됩니다.

## <a name="connect-from-outside-the-container"></a> 컨테이너 외부에서 연결

SQL 연결을 지 원하는 모든 외부 Linux, Windows 또는 macOS 도구에서 Azure SQL Edge 인스턴스에 대해 SQL 쿼리를 연결 하 고 실행할 수 있습니다. 외부에서 SQL Edge 컨테이너에 연결 하는 방법에 대 한 자세한 내용은 [AZURE Sql Edge 연결 및 쿼리](https://docs.microsoft.com/azure/azure-sql-edge/connect)를 참조 하세요.

이 빠른 시작에서는 IoT Edge 디바이스에 SQL Edge 모듈을 배포했습니다. 

## <a name="next-steps"></a>다음 단계

- [SQL Edge에 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge의 데이터 스트리밍](stream-data.md)
