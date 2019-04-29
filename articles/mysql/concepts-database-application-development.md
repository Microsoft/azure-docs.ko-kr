---
title: Azure Database for MySQL에 대한 데이터베이스 애플리케이션 개발 개요
description: 개발자가 MySQL용 Azure 데이터베이스에 연결하기 위한 애플리케이션 코드를 작성할 때 따라야 하는 디자인 고려 사항을 소개합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 946f7011c51b7c6844e023d03e01e4c2043d2578
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615649"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 애플리케이션 개발 개요 
이 문서에서는 개발자가 Azure Database for MySQL에 연결하기 위한 애플리케이션 코드를 작성할 때 따라야 하는 디자인 고려 사항을 설명합니다. 

> [!TIP]
> 서버를 만들고, 서버 기반 방화벽을 만들고, 서버 속성을 보고, 데이터베이스를 만들고, 워크벤치 및 mysql.exe를 사용하여 연결 및 쿼리하는 방법을 보여 주는 자습서를 보려면 [첫 번째 Azure Database for MySQL 데이터베이스 디자인](tutorial-design-database-using-portal.md)을 참조하세요.

## <a name="language-and-platform"></a>언어 및 플랫폼
다양한 프로그래밍 언어 및 플랫폼에 대한 코드 샘플을 사용할 수 있습니다. 다음에서 코드 샘플에 대한 링크를 찾을 수 있습니다. [Azure Database for MySQL에 연결하는 데 사용되는 연결 라이브러리](concepts-connection-libraries.md)

## <a name="tools"></a>도구
MySQL용 Azure 데이터베이스는 워크벤치와 같은 MySQL 공통 관리 도구 또는 mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql) 등과 같은 MySQL 유틸리티와 호환되는 MySQL 커뮤니티 버전을 사용합니다. 또한 Azure Portal, Azure CLI, REST API를 사용하여 데이터베이스 서비스와 상호 작용할 수도 있습니다.

## <a name="resource-limitations"></a>리소스 제한
Azure Database for MySQL은 다음 두 가지 메커니즘을 사용하여 서버에서 사용할 수 있는 리소스를 관리합니다. 
- 리소스 관리.
- 제한 적용

## <a name="security"></a>보안
Azure Database for MySQL은 액세스를 제한하고, 데이터를 보호하고, 사용자 및 역할을 구성하고, MySQL 데이터베이스에 대한 작업을 모니터링하기 위한 리소스를 제공합니다.

## <a name="authentication"></a>Authentication
Azure Database for MySQL은 사용자 및 로그인의 서버 인증을 지원합니다.

## <a name="resiliency"></a>복원력
MySQL 데이터베이스에 연결하는 동안 일시적인 오류가 발생하면, 코드는 호출을 다시 시도해야 합니다. 여러 클라이언트가 재시도를 동시에 수행하여 SQL 데이터베이스가 채워지지 않도록 재시도 논리에 백오프 논리를 사용하는 것이 좋습니다.

- 코드 샘플: 재시도 논리를 설명하는 코드 샘플을 보려면 다음에서 다양한 언어의 샘플을 참조하세요. [Azure Database for MySQL에 연결하는 데 사용되는 연결 라이브러리](concepts-connection-libraries.md)

## <a name="managing-connections"></a>연결 관리
데이터베이스 연결은 제한된 리소스이므로 성능을 향상시키려면 MySQL 데이터베이스에 액세스할 때 적절한 연결을 사용하는 것이 좋습니다.
- 연결 풀링 또는 영구 연결을 사용하여 데이터베이스에 액세스합니다.
- 수명이 짧은 연결을 사용하여 데이터베이스에 액세스합니다. 
- 연결 시도 시 애플리케이션의 재시도 논리를 사용하여 동시 연결이 허용 된 최대값에 도달하여 발생한 오류를 잡아냅니다. 재시도 논리에는 짧은 지연 시간을 설정한 후, 추가 연결 시도가 있기 전에 임의 시간 동안 기다립니다.