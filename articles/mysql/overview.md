---
title: 개요 - Azure Database for MySQL
description: MySQL 커뮤니티 버전을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MySQL 서비스에 대해 알아봅니다.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763663"
---
# <a name="what-is-azure-database-for-mysql"></a>MySQL용 Azure Database란?

Azure Database for MySQL은 [MySQL 커뮤니티 버전](https://www.mysql.com/products/community/)(GPLv2 라이선스에서 사용 가능) 데이터베이스 엔진(버전 5.6, 5.7 및 8.0)을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. MySQL용 Azure Database는 다음과 같은 기능을 제공합니다.

- 추가 비용 없이 기본 제공되는 고가용성
- 예측 가능한 성능, 종량제 가격 책정 사용
- 몇 초 이내 필요에 따라 크기 조정
- 중요한 미사용 데이터 및 사용 데이터 보호
- 최대 35일 동안 자동 백업 및 지정 시간 복원
- 엔터프라이즈급 보안 및 규정 준수

이러한 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 그러면 가상 머신과 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 앱 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 또한 오픈 소스 도구 및 선택한 플랫폼을 사용하여 애플리케이션을 개발하고 새로운 기술을 배울 필요 없이 비즈니스에서 요구하는 속도 및 효율성을 계속 제공할 수 있습니다.

![MySQL용 Azure Database 개념 다이어그램](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

이 문서에서는 세부 정보를 찾는 링크를 통해 성능, 확장성 및 관리 효율성과 관련된 MySQL용 Azure Database의 핵심 개념과 기능을 소개합니다. 이러한 빠른 시작을 참조하여 다음 항목을 시작하세요.

- [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용한 MySQL용 Azure Database 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)

일련의 Azure CLI 샘플은 다음을 참조하세요.

- [MySQL용 Azure Database에 대한 Azure CLI 샘플](sample-scripts-azure-cli.md)

## <a name="automated-patching"></a>자동화된 패치
서비스는 기본 하드웨어, OS 및 데이터베이스 엔진의 자동화된 패치를 수행합니다. 패치에는 기본 하드웨어, OS 및 데이터베이스 엔진에 대한 보안 및 소프트웨어 업데이트가 포함됩니다. MySQL 엔진의 경우 부 버전 업그레이드는 자동으로 적용되며 패치 릴리스의 일부로 포함됩니다. 커뮤니티에서 부 버전을 릴리스하면 서비스 테스트 주기의 일부로 자동으로 통합됩니다. 부 버전의 테스트는 MySQL의 정식 작업 중 일부에 대해 수행됩니다. MySQL 엔진의 부 버전 릴리스는 안정성(충돌 없음), 가용성, 보안 및 성능에 대해 평가됩니다. 모든 부 버전이 서비스에서 프로덕션으로 릴리스되는 것은 아니지만 버그 수정의 중요도와 새 증분 값에 따라 평가됩니다. 이는 새 증분 값 간에 적절한 균형을 유지하고 안정성을 위해 시스템의 변수를 최소화하기 위한 것입니다. 패치를 적용하는 데 필요한 사용자 작업 또는 구성 설정은 없습니다. 패치 주기는 페이로드의 중요도에 따라 관리되는 서비스입니다. 일반적으로 서비스는 연속 통합 및 릴리스의 일부로 월별 릴리스 일정을 따릅니다. 사용자는 [계획된 유지 관리 알림](concepts-monitoring.md)을 구독하여 이벤트 72시간 전에 예정된 유지 관리 알림을 받을 수 있습니다.

## <a name="adjust-performance-and-scale-within-seconds"></a>몇 초 이내 성능 및 규모 조정
MySQL는 Azure Database는 다음과 같은 여러 서비스 계층을 제공합니다. 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층 중 하나에서 만들 수 있습니다. 각 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 다양한 성능 및 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은  [가격 책정 계층](concepts-service-tiers.md)을 참조하세요.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고
기능을 사용하고 해제할 때를 어떻게 결정하나요? vCore 수에 따른 성능 등급과 결합된 기본 제공 성능 모니터링 및 경고 기능을 사용합니다. 이러한 도구를 사용하면 현재 또는 예상되는 성능 요구 사항에 따라 vCore 수를 확장 또는 축소함으로써 발생하는 효과를 신속하게 평가할 수 있습니다. 자세한 내용은 [경고](howto-alert-on-metric.md)를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지
Azure의 업계 선도적인 99.99% 가용성 SLA(서비스 수준 약정)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 다른 방법으로 구입 또는 설계, 구축 및 관리해야 할 수도 있는 기본 제공 보안, 내결함성 및 데이터 보호를 모든 MySQL 서버용 Azure Database에서 활용합니다. MySQL용 Azure Database에서 특정 시점 복원을 사용하여 서버를 최대 35일 전의 상태로 복원할 수 있습니다.

## <a name="secure-your-data"></a>데이터 보호
Azure Database 서비스는 MySQL용 Azure Database가 액세스를 제한하고, 미사용 및 사용 중인 데이터를 보호하고, 작업을 모니터링하는 데 도움이 되는 기능을 사용하여 보관하도록 데이터 보안을 유지해왔습니다. Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://www.microsoft.com/trustcenter/security)를 방문하세요. Azure Database for MySQL 보안 기능에 대한 자세한 내용은 [보안 개요](concepts-security.md)를 참조하세요.

## <a name="contacts"></a>연락처
Azure Database for MySQL 작업에 대해 궁금한 점이나 제안하고 싶은 의견이 있으면 Azure Database for MySQL 팀([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))으로 이메일을 보내주세요. 이 이메일 주소는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계
이제 Azure Database for MySQL에 대한 소개를 읽고 질문 "Azure Database for MySQL이란?"에 답변했습니다. 다음을 수행할 준비가 되었습니다.

- 비용 비교 및 계산기는 가격 책정 페이지를 참조하세요. [가격](https://azure.microsoft.com/pricing/details/mysql/)
- 첫 번째 서버를 만들어서 시작합니다. [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)
- 기본 설정된 언어를 사용하여 첫 번째 앱을 빌드합니다.
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
