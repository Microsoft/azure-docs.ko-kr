---
title: 기존 Azure App Service를 Azure Database for MySQL에 연결
description: 기존 Azure App Service를 Azure Database for MySQL에 적절하게 연결하는 방법에 대한 지침
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: eb2fee7c76bcf29aee2dcd70d7975d7631bb23f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459208"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>기존 Azure App Service를 Azure Database for MySQL 서버에 연결
이 항목에서는 기존 Azure App Service를 Azure Database for MySQL 서버에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에
[Azure Portal](https://portal.azure.com)에 로그인합니다. Azure Database for MySQL 서버를 만듭니다. 자세한 내용은 [포털에서 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 또는 [CLI를 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)를 참조하세요.

현재 Azure App Service에서 Azure Database for MySQL로의 액세스를 가능하게 하는 두 가지 솔루션이 있습니다. 두 솔루션 모두 서버 수준 방화벽 규칙 설정 작업이 포함됩니다.

## <a name="solution-1---allow-azure-services"></a>해결 방법 1 - Azure 서비스 허용
Azure Database for MySQL은 데이터를 보호하는 방화벽을 사용하여 액세스 보안을 제공합니다. Azure App Service에서 Azure Database for MySQL 서버로 연결할 때 App Service의 아웃바운드 IP는 기본적으로 동적이라는 것을 염두에 두어야 합니다. "Azure 서비스에 대한 액세스 허용" 옵션을 선택하면 App Service가 MySQL 서버에 연결할 수 있습니다.

1. MySQL 서버 블레이드의 설정 머리글에서 **연결 보안**을 클릭하여 MySQL용 Azure Database에 대한 연결 보안 블레이드를 엽니다.

   ![Azure Portal - 보안 연결 클릭](./media/howto-connect-webapp/1-connection-security.png)

2. **Azure 서비스에 대한 액세스 허용**에서 **설정**, **저장**을 차례로 선택합니다.
   ![Azure Portal - Azure 액세스 허용](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>솔루션 2 - 아웃바운드 IP를 명시적으로 허용하는 방화벽 규칙 만들기
Azure App Service의 모든 아웃바운드 IP를 명시적으로 추가할 수 있습니다.

1. App Service 속성 블레이드에서 **아웃바운드 IP 주소**를 확인합니다.

   ![Azure Portal - 아웃바운드 IP 확인](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. MySQL 연결 보안 블레이드에서 아웃바운드 IP를 하나씩 추가합니다.

   ![Azure Portal - 명시적 IP 추가](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. 잊지 말고 방화벽 규칙을 **저장**하세요.

Azure App Service에서는 시간이 지나도 IP 주소를 동일하게 유지하려고 하지만 경우에 따라 IP 주소가 변경될 수도 있습니다. 예를 들어 수용 작업량을 늘리기 위해 앱 재활용 또는 크기 조정 작업이 발생하거나 Azure 지역 데이터 센터에 새 컴퓨터가 추가되는 경우에 발생할 수 있습니다. IP 주소가 변경되면 앱에서 더 이상 MySQL 서버에 연결할 수 없는 경우 가동 중지가 발생할 수 있습니다. 이전 솔루션 중 하나를 선택할 때 이 고려 사항을 염두에 둡니다.

## <a name="ssl-configuration"></a>SSL 구성
Azure Database for MySQL은 기본적으로 SSL을 사용하도록 설정됩니다. 애플리케이션에서 데이터베이스에 연결할 때 SSL을 사용하지 않는 경우 MySQL 서버에서 SSL을 사용하지 않도록 설정해야 합니다. SSL을 구성하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에 SSL 사용](howto-configure-ssl.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
연결 문자열에 대한 자세한 내용은 [연결 문자열](howto-connection-string.md)을 참조하세요.
