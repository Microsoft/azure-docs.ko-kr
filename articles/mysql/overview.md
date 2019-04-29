---
title: Azure Database for MySQL 관계형 데이터베이스 서비스 개요
description: MySQL용 Azure Database 관계형 데이터베이스 서비스 개요입니다.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2852cab05fab8e15b7e60a22f54cc866d2f0f178
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226263"
---
# <a name="what-is-azure-database-for-mysql"></a>MySQL용 Azure Database란?

Azure Database for MySQL은 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스를 [MySQL Community Edition](https://www.mysql.com/products/community/) (GPLv2 라이선스로 사용 가능) 데이터베이스 엔진, 5.6 및 5.7 버전입니다. MySQL용 Azure Database는 다음과 같은 기능을 제공합니다.

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

## <a name="adjust-performance-and-scale-within-seconds"></a>몇 초 이내 성능 및 규모 조정
MySQL는 Azure Database는 다음과 같은 여러 서비스 계층을 제공합니다. 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층 중 하나에서 만들 수 있습니다. 각 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 다양한 성능 및 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은  [가격 책정 계층](concepts-service-tiers.md)을 참조하세요.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고
기능을 사용하고 해제할 때를 어떻게 결정하나요? vCore 수에 따른 성능 등급과 결합된 기본 제공 성능 모니터링 및 경고 기능을 사용합니다. 이러한 도구를 사용하면 현재 또는 예상되는 성능 요구 사항에 따라 vCore 수를 확장 또는 축소함으로써 발생하는 효과를 신속하게 평가할 수 있습니다. 자세한 내용은 [경고](howto-alert-on-metric.md)를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지
Azure의 업계 선도적인 99.99% 가용성 SLA(서비스 수준 약정)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 다른 방법으로 구입 또는 설계, 구축 및 관리해야 할 수도 있는 기본 제공 보안, 내결함성 및 데이터 보호를 모든 MySQL 서버용 Azure Database에서 활용합니다. MySQL용 Azure Database에서 특정 시점 복원을 사용하여 서버를 최대 35일 전의 상태로 복원할 수 있습니다.

## <a name="secure-your-data"></a>데이터 보호
Azure Database 서비스는 MySQL용 Azure Database가 액세스를 제한하고, 미사용 및 사용 중인 데이터를 보호하고, 작업을 모니터링하는 데 도움이 되는 기능을 사용하여 보관하도록 데이터 보안을 유지해왔습니다. Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://www.microsoft.com/en-us/trustcenter/security)를 방문하세요.

Azure Database for MySQL 서비스는 미사용 데이터에 대해 저장소 암호화를 사용합니다. 백업을 비롯한 데이터는 디스크에서 암호화됩니다. 단, 쿼리를 실행하는 동안 엔진에서 생성하는 임시 파일은 예외입니다. 서비스는 Azure Storage 암호화에 포함된 AES 256비트 암호화를 사용하며 키는 시스템에서 관리합니다. 저장소 암호화는 항상 켜져 있고 해제할 수 없습니다.

기본적으로 MySQL용 Azure 데이터베이스 서비스는 네트워크를 통해 이동하는 데이터에 대해 [SSL 연결 보안](./concepts-ssl-connection-security.md)을 요구하도록 구성됩니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 "메시지 가로채기(man in the middle)" 공격으로부터 보호할 수 있습니다. 필요에 따라 클라이언트 애플리케이션이 SSL 연결을 지원하지 않을 경우 데이터베이스 서비스에 연결하기 위해 SSL을 요구하지 않도록 설정할 수 있습니다.

## <a name="contacts"></a>연락처
Azure Database for MySQL 작업에 대해 궁금한 점이나 제안하고 싶은 의견이 있으면 Azure Database for MySQL 팀([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))으로 이메일을 보내주세요. 이는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계
이제 MySQL용 Azure Database에 대한 소개를 읽고 질문 "MySQL용 Azure Database란?"에 답변했으므로 다음에 대한 준비가 되었습니다.

- 비용 비교 및 계산기는 가격 책정 페이지를 참조하세요. [가격](https://azure.microsoft.com/pricing/details/mysql/)
- 첫 번째 서버를 만들어서 시작합니다. [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)
- 기본 설정된 언어를 사용하여 첫 번째 앱을 빌드합니다. [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
