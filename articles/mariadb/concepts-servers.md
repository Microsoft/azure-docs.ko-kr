---
title: 서버 - Azure Database for MariaDB
description: 이 항목에서는 Azure Database for MariaDB 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a5cd55576d505114c15028cf637114f3baf7668
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627985"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 서버 개념
이 문서에서는 Azure Database for MariaDB 서버를 사용할 때의 고려 사항 및 지침을 제공합니다.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버란?

여러 데이터베이스를 중앙에서 관리할 수 있는 위치인 Azure Database for MariaDB 서버는 온-프레미스 환경에서도 흔히 사용 중일 수 있는 구문과 동일한 MariaDB 서버 구문입니다. 구체적으로 설명하자면, Azure Database for MariaDB 서비스는 관리되는 서비스이고, 성능을 보장하며, 서버 수준에서 액세스 권한과 기능을 표시합니다.

Azure Database for MariaDB 서버의 특징은 다음과 같습니다.

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 서버 및 데이터베이스 액세스에 대한 연결 엔드포인트를 제공합니다.
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- MariaDB 엔진 버전 10.2에서 사용 가능합니다. 자세한 내용은 [지원되는 Azure Database for MariaDB 데이터베이스 버전](./concepts-supported-versions.md)을 참조하세요.

Azure Database for MariaDB 서버 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 가격 책정 계층, vCore, 스토리지(GB)의 구성에 따라 서버별로 구성됩니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버를 보호하는 방법

다음과 같은 요소가 데이터베이스에 안전하게 액세스할 수 있도록 도와줍니다.

| 보안 개념 | Description |
| :-- | :-- |
| **인증 및 권한 부여** | Azure Database for MariaDB 서버는 네이티브 MySQL 인증을 지원합니다. 서버의 관리자 로그인을 사용하여 서버에 연결하고 인증을 받을 수 있습니다. |
| **프로토콜** | 이 서비스는 MySQL에서 사용되는 메시지 기반 프로토콜을 지원합니다. |
| **TCP/IP** | 이 프로토콜은 TCP/IP 및 Unix 도메인 소켓을 통해 지원됩니다. |
| **방화벽** | 데이터를 보호하기 위해, 방화벽 규칙은 사용자가 권한 있는 컴퓨터를 지정하기 전에는 데이터베이스 서버에 대한 모든 액세스를 차단합니다. [ 서버 방화벽 규칙](./concepts-firewall-rules.md)을 참조하세요. |
| **SSL** | 이 서비스는 애플리케이션 및 데이터베이스 서버 간의 SSL 연결 적용을 지원합니다. [Azure Database for MariaDB에 안전하게 연결하기 위한 사용자 애플리케이션의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요. |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Azure Database for MariaDB 시작/중지(미리 보기)
Azure Database for MariaDB에서는 사용하지 않을 때 서버를 **중지** 하고 작업을 다시 시작할 때 서버를 **시작** 하는 기능을 제공합니다. 이 기능은 기본적으로 데이터베이스 서버의 비용을 절감하고, 사용 중일 때만 리소스 비용을 지불하기 위해 수행됩니다. 이 점은 개발-테스트 워크로드의 경우 그리고 하루 중 일부 시간만 서버를 사용하는 경우에 더 중요해집니다. 서버를 중지하면 모든 활성 연결이 삭제됩니다. 나중에 서버를 다시 온라인 상태로 전환하려는 경우 [Azure Portal](../mysql/how-to-stop-start-server.md) 또는 [CLI](../mysql/how-to-stop-start-server.md)를 사용하면 됩니다.

서버가 **중지됨** 상태이면 서버 컴퓨팅에 요금이 청구되지 않습니다. 그러나 서버가 다시 시작될 때 데이터 파일을 사용할 수 있도록 서버의 스토리지가 그대로 남아 있기 때문에 스토리지 요금이 계속 청구됩니다.

> [!IMPORTANT]
> 서버를 **중지** 하면 다음 7일 동안 계속 해당 상태가 유지됩니다. 이 시간 동안 수동으로 **시작** 하지 않으면 서버는 7일이 끝나는 시점에 자동으로 시작됩니다. 서버를 사용하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

서버가 중지된 동안에는 서버에서 관리 작업을 수행할 수 없습니다. 서버에서 구성 설정을 변경하려면 [서버를 시작](../mysql/how-to-stop-start-server.md)해야 합니다.

### <a name="limitations-of-stopstart-operation"></a>중지/시작 작업의 제한 사항
- 읽기 복제본 구성(원본과 복제본 모두)에서는 지원되지 않습니다.

## <a name="how-do-i-manage-a-server"></a>서버는 어떻게 관리해야 하나요?
Azure Portal 또는 Azure CLI를 사용하여 Azure Database for MariaDB 서버를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 서비스 개요를 보려면 [Azure Database for MariaDB 개요](./overview.md)를 참조하세요.
- **서비스 계층** 에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
