---
title: Azure Database for MariaDB에서 지원되는 버전
description: Azure Database for MariaDB에서 지원되는 버전에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935554"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>지원되는 Azure Database for MariaDB 서버 버전
Azure Database for MariaDB는 InnoDB 엔진을 사용하여 오픈 소스 [MariaDB 서버](https://downloads.mariadb.org/)에서 개발되었습니다. Azure Database for MariaDB는 현재 다음 버전을 지원합니다.

## <a name="mariadb-version-10217"></a>MariaDB 버전 10.2.17
MariaDB 10.2.17의 향상된 기능 및 수정 내용에 대한 자세한 내용은 [MariaDB 설명서](https://downloads.mariadb.org/mariadb/10.2.17/)를 참조하세요.

> [!NOTE]
> 서비스에서 게이트웨이를 사용하여 새 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MariaDB 서버 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MariaDB 버전을 표시합니다. MariaDB 서버 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
서비스는 부 버전 업데이트에 대한 패치를 자동으로 관리합니다.

## <a name="next-steps"></a>다음 단계
**서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은 [서비스 계층](./concepts-pricing-tiers.md)을 참조하세요.
