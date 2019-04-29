---
title: Azure Database for MariaDB에 대한 연결 문제 해결 | Microsoft Docs
description: Azure Database for MariaDB에 대한 연결 문제를 해결하는 방법을 알아봅니다.
keywords: MariaDB 연결, 연결 문자열, 연결 문제, 일시적 오류, 연결 오류
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: f5c5a980a99bac3bf02eaaf9c072861ca753d449
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038499"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대한 연결 문제 해결

연결 문제는 다음과 같은 다양한 작업으로 인해 발생할 수 있습니다.

* 방화벽 설정
* 연결 제한 시간
* 잘못된 로그인 정보
* 일부 Azure Database for MariaDB 리소스에서 최대 제한 도달
* 서비스 인프라 관련 문제
* 서비스에서 유지 관리 수행 중
* vCores 수를 조정하거나 다른 서비스 계층으로 이동하여 서버의 계산 할당 변경

일반적으로 Azure Database for MariaDB에 대한 연결 문제는 다음과 같이 분류할 수 있습니다.

* 일시적 오류(단기 또는 일시적)
* 영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)

## <a name="troubleshoot-transient-errors"></a>일시적인 오류 문제 해결

유지 관리가 수행되거나 시스템에 하드웨어 또는 소프트웨어에 오류가 발생하거나 서버의 vCore 수 또는 서비스 계층을 변경할 때 일시적 오류가 발생합니다. Azure Database for MariaDB 서비스는 고가용성을 기본적으로 제공하며, 이러한 유형의 문제를 자동으로 완화하도록 설계되었습니다. 그러나 애플리케이션은 일반적으로 최대 60초 미만의 짧은 기간 동안 서버에 대한 연결이 끊어집니다. 대용량 트랜잭션으로 인해 장기 실행 복구가 발생하는 경우와 같이 경우에 따라 일부 이벤트를 완화하는 데 시간이 더 걸릴 수 있습니다.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>일시적인 연결 문제를 해결하는 단계

1. [Microsoft Azure 서비스 대시보드](https://azure.microsoft.com/status)에서 애플리케이션이 오류를 보고한 시간 동안 발생한 알려진 중단을 모두 확인합니다.
2. Azure Database for MariaDB와 같은 클라우드 서비스에 연결하는 애플리케이션은 일시적 오류를 예상하고 이러한 이벤트를 사용자에게 애플리케이션 오류로 표시하는 대신 해당 오류를 처리하는 재시도 논리를 구현해야 합니다. 일시적 오류 처리를 위한 모범 사례 및 설계 지침은 [Handling of transient connectivity errors for Azure Database for MariaDB](concepts-connectivity.md)(Azure Database for MariaDB에 대한 일시적 연결 오류 처리)를 검토하세요.
3. 서버에서 리소스 제한에 도달하면 오류가 일시적 연결 문제로 보일 수 있습니다. [Azure Database for MariaDB의 제한 사항](concepts-limits.md)을 참조하세요.
4. 연결 문제가 계속 발생하거나 애플리케이션에서 오류가 발생하는 기간이 60초를 초과하는 경우 또는 특정일에 오류가 여러 번 발생하는 경우에는 **Azure 지원** 사이트에서 [지원 받기](https://azure.microsoft.com/support/options)를 선택하여 Azure 지원 요청을 접수합니다.

## <a name="troubleshoot-persistent-errors"></a>영구 오류 문제 해결

애플리케이션에서 Azure Database for MariaDB 연결에 계속 실패하는 경우 일반적으로 다음 문제 중 하나를 나타낼 수 있습니다.

* 방화벽 구성: Azure Database for MariaDB 서버 또는 클라이언트 쪽 방화벽에서 연결을 차단하고 있습니다.
* 클라이언트 쪽 네트워크 재구성: 새 IP 주소 또는 프록시 서버가 추가되었습니다.
* 사용자 오류: 예를 들어, 있습니다 수 잘못 입력 했을 연결 문자열 또는 누락 된 서버 이름과 같이 연결 매개 변수  *\@servername* 사용자 이름에는 접미사입니다.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>영구적인 연결 문제를 해결하는 단계

1. 클라이언트 IP 주소를 허용하도록 [방화벽 규칙](howto-manage-firewall-portal.md) 을 설정합니다. 임시 테스트 용도로만 목적으로만 0.0.0.0을 시작 IP 주소로 사용하고 255.255.255.255를 끝 IP 주소로 사용하여 방화벽 규칙을 설정합니다. 이렇게 하면 서버가 모든 IP 주소로 열립니다. 이렇게 해서 연결 문제가 해결되면 이 규칙을 제거하고 적절하게 제한된 IP 주소 또는 주소 범위에 대해 방화벽 규칙을 만듭니다.
2. 클라이언트와 인터넷 간의 모든 방화벽에서 아웃바운드 연결을 위해 3306 포트가 열려 있는지 확인합니다.
3. 연결 문자열 및 기타 연결 설정을 확인합니다. [Azure Database for MariaDB에 애플리케이션을 연결하는 방법](howto-connection-string.md)을 검토합니다.
4. 대시보드에서 서비스 상태를 확인합니다. 지역 가동 중단이 있다고 생각되는 경우 새 영역으로 복구하는 단계는 [Azure Database for MariaDB의 비즈니스 연속성 개요](concepts-business-continuity.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Handling of transient connectivity errors for Azure Database for MariaDB](concepts-connectivity.md)(Azure Database for MariaDB에 대한 일시적 연결 오류 처리)
