---
title: Windows 구성 Java 앱-Azure App Service | Microsoft Docs
description: Java 앱이 Azure App Service의 기본 Windows 인스턴스에서 실행 되도록 구성 하는 방법에 알아봅니다.
keywords: azure 앱 서비스, 웹 앱, windows, os, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: efeab014c7d92a6c19d516a121fbc4436925be75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850991"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>구성 된 Windows Azure App Service 용 Java 앱

Java Standard Edition (SE)는 완전히 관리 되는 Windows 기반 서비스에 웹 응용 프로그램 패키지 또는 azure App Service에는 Java 개발자가 신속 하 게 빌드, 배포 및 해당 Tomcat을 확장할 수 있습니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드는 주요 개념 및 App Service에서 사용 하 여 Java 개발자를 위한 지침을 제공 합니다. Azure App Service를 사용한 경험이 없는 경우를 읽어야 하는 [Java 빠른 시작](app-service-web-get-started-java.md) 첫 번째입니다. Java 개발에 국한 되지 않는 App Service를 사용 하는 방법에 대 한 일반적인 질문에 답변해 드립니다 합니다 [앱 서비스 Windows FAQ](faq-configuration-and-management.md)합니다.

> [!NOTE]
> 원하는 항목을 찾을 수 없나요? 참조 하십시오 합니다 [Windows OSS FAQ](faq-configuration-and-management.md) 또는 [Java Linux 구성 가이드](containers/app-service-linux-java.md) 배포 및 Java 응용 프로그램 보안에 대 한 정보에 대 한 합니다.

## <a name="configuring-tomcat"></a>Tomcat 구성

Tomcat의 편집 하려면 `server.xml` 또는 다른 구성 파일을 포털에서 Tomcat 주요 버전의 참고를 먼저 수행 합니다.

1. 버전에 대 한 실행 하 여 Tomcat 홈 디렉터리를 찾을 `env` 명령입니다. 시작 하는 환경 변수에 대해 검색 `AZURE_TOMCAT`주요 버전과 일치 하 고 있습니다. 예를 들어 `AZURE_TOMCAT85_HOME` Tomcat 8.5에 대 한 Tomcat 디렉터리를 가리킵니다.
1. Tomcat 홈 디렉터리 버전을 식별 한 후 구성 디렉터리로 복사 `D:\home`합니다. 예를 들어 경우 `AZURE_TOMCAT85_HOME` 의 값이 `D:\Program Files (x86)\apache-tomcat-8.5.37`를 복사 구성 디렉터리의 전체 경로 `D:\home\tomcat\conf`합니다.

마지막으로, App Service를 다시 시작합니다. 배포로 이동 해야 `D:\home\site\wwwroot\webapps` 이전과 동일 하 게 합니다.

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다.

주 버전 업데이트는 Windows에 대 한 Azure App Service에서 새 런타임 옵션을 통해 제공 됩니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

에 대 한 제품 지원 합니다 [Azul Zulu JDK Azure 지원](https://www.azul.com/downloads/azure-only/zulu/) Microsoft를 통해 사용할 수 있는 경우 Azure에 대 한 개발 또는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 사용 하 여를 [Azure 지원 계획을 정규화 된](https://azure.microsoft.com/support/plans/)합니다.

### <a name="runtime-support"></a>런타임 지원

개발자는 [정규화된 지원 계획](https://azure.microsoft.com/support/plans/)이 있는 경우 Azure 지원을 통해 Azul Zulu JDK 관련 [문제를 제기](/azure/azure-supportability/how-to-create-azure-support-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 항목에서는 Windows에서 Java 런타임 설명은 Azure App Service에 대 한 지원 제공합니다.

- Azure App Service 참조를 사용 하 여 웹 응용 프로그램 호스팅에 대해 자세히 알아보려면 [App Service 개요](overview.md)합니다.
- Azure 개발에 대 한 Java에 대 한 정보를 참조 하세요. [Java 개발자 센터에 대 한 Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)합니다.
