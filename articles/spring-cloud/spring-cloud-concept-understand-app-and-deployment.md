---
title: Azure 스프링 클라우드의 앱 및 배포 이해
description: Azure 스프링 클라우드의 응용 프로그램과 배포의 차이점
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 81e1925810f374da6f02bf6c3a013b00b5bb9a2c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264060"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Azure 스프링 클라우드의 앱 및 배포 이해

**앱** 및 **배포** 는 Azure 스프링 클라우드의 리소스 모델에 있는 두 가지 주요 개념입니다. Azure 스프링 클라우드에서 *앱* 은 하나의 비즈니스 앱 또는 하나의 마이크로 서비스를 추상화 한 것입니다.  *응용 프로그램이* *배포*에서 실행 될 때 하나의 코드 또는 이진 버전이 배포 됩니다.

 ![앱 및 배포](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Azure 스프링 클라우드 표준 계층을 사용 하면 한 앱에서 하나의 프로덕션 배포와 하나의 스테이징 배포를 수행할 수 있으므로,이를 통해 파란색/녹색 배포를 쉽게 수행할 수 있습니다.

## <a name="app"></a>앱
다음 기능/속성은 앱 수준에 정의 됩니다.

| 열거형 | 정의 |
|:--|:----------------|
| 공용</br>엔드포인트 | 앱에 액세스 하기 위한 URL입니다. |
| 사용자 지정</br>도메인 | 사용자 지정 도메인을 보호 하는 CNAME 레코드 |
| 서비스</br>바인딩 | function.js파일 및 *ServiceBusTrigger* 특성에 설정 된 바인딩 구성 속성 |
| 관리 대상</br>ID | Azure Active Directory로 관리 되는 id를 사용 하면 앱에서 Azure Key Vault 같은 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다. |
| 영구적</br>스토리지 | 앱을 다시 시작 하지 않아도 데이터를 유지할 수 있도록 설정 |

## <a name="deployment"></a>배포

다음 기능/속성은 배포 수준에서 정의 되며 프로덕션/스테이징 배포를 교체할 때 교환 됩니다.

| 열거형 | 정의 |
|:--|:----------------|
| CPU | 앱 인스턴스당 vcore 수 |
| 메모리 | 배포를 확장 또는 축소 하기 위해 메모리를 할당 하는 설정 |
| 인스턴스</br>개수 | 앱 인스턴스 수, 수동 또는 자동으로 설정 |
| 자동 크기 조정 | 미리 정의 된 규칙 및 일정에 따라 자동으로 인스턴스 개수 조정 |
| JVM</br>옵션 | 설정: JAVA_OPTS |
| Environment</br>variables | 전체 Azure 스프링 클라우드 환경에 적용 되는 설정 |
| 런타임</br>버전 | Java 8/Java 11|

## <a name="restrictions"></a>제한

* **앱은 프로덕션 배포를 하나 포함 해야**합니다. 프로덕션 배포 삭제는 API에 의해 차단 됩니다. 삭제 하기 전에 준비로 바꾸어야 합니다.
* **앱에는 최대 두 개의 배포가 있을 수 있습니다**. 두 개 이상의 배포를 만드는 작업은 API에 의해 차단 됩니다. 기존 프로덕션 또는 스테이징 배포에 새 이진 파일을 배포 합니다.
* **배포 관리는 기본 계층에서 사용할 수 없습니다**. 파란색-녹색 배포 기능을 사용 하려면 표준 계층을 사용 하세요.

## <a name="see-also"></a>참고 항목
* [Azure 스프링 클라우드에서 스테이징 환경 설정](spring-cloud-howto-staging-environment.md)