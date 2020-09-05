---
title: '자습서: Azure Spring Cloud의 애플리케이션 크기 조정 | Microsoft Docs'
description: Azure Portal에서 Azure Spring Cloud를 사용하여 애플리케이션 크기를 조정하는 방법에 대해 알아봅니다.
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: c3c660c5e301081ee82a12275b6e9125f0fc781b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289820"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>자습서: Azure Spring Cloud의 애플리케이션 크기 조정

이 자습서에서는 Azure Portal에서 Azure Spring Cloud 대시보드를 사용하여 마이크로서비스 애플리케이션의 크기를 조정하는 방법을 보여줍니다.

vCPU(가상 CPU) 수와 메모리 양을 수정하여 애플리케이션을 확장하고 축소합니다. 애플리케이션 인스턴스의 수를 수정하여 애플리케이션의 크기를 조정합니다.

작업이 완료되면 서비스에서 각 애플리케이션에 대해 빠르게 수동 조정을 수행하는 방법을 알게 됩니다. 크기 조정은 몇 초 안에 적용되며 코드를 변경하거나 다시 배포할 필요가 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 절차를 따르려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
* 배포된 Azure Spring Cloud 서비스 인스턴스  [Azure CLI를 통한 앱 배포를 위한 빠른 시작](spring-cloud-quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Azure Portal의 크기 조정 페이지로 이동합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Spring Cloud **개요** 페이지로 이동합니다.

1. 서비스가 포함된 리소스 그룹을 선택합니다.

1. 페이지의 왼쪽에 있는 메뉴에서 **설정** 아래에 있는 **앱** 탭을 선택합니다.

1. 크기를 조정하려는 애플리케이션을 선택합니다. 이 예제에서는 **account-service**라는 애플리케이션을 선택합니다. 그러면 애플리케이션의 **개요** 페이지가 표시됩니다.

1. 페이지의 왼쪽에 있는 메뉴에서 **설정** 아래에 있는 **크기 조정** 탭으로 이동합니다. 다음 섹션에 표시된 크기 조정 특성에 대한 옵션이 표시됩니다.

## <a name="scale-your-application"></a>애플리케이션 크기 조정

크기 조정 특성을 수정하는 경우 다음 사항을 염두에 두어야 합니다.

* **CPU**: 애플리케이션 인스턴스당 최대 CPU 수는 4개입니다. 애플리케이션에 대한 총 CPU 수는 여기에 애플리케이션 인스턴스 수를 곱한 값으로 설정됩니다.

* **메모리/GB**: 애플리케이션 인스턴스당 최대 메모리 양은 8GB입니다. 애플리케이션에 대한 총 메모리 양은 여기에 애플리케이션 인스턴스 수를 곱한 값으로 설정됩니다.

* **앱 인스턴스 수**: 표준 계층에서 최대 20개 인스턴스까지 확장할 수 있습니다. 이 값은 마이크로서비스 애플리케이션의 개별 실행 인스턴스 수를 변경합니다.

**저장**을 선택하여 크기 조정 설정을 적용해야 합니다.

![Azure Portal의 크기 조정 서비스](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

몇 초 후 변경한 내용이 **개요** 페이지에 표시되며, 자세한 내용은 **애플리케이션 인스턴스** 탭에서 확인할 수 있습니다. 크기 조정에는 코드를 변경하거나 배포를 다시 수행할 필요가 없습니다.

## <a name="upgrade-to-the-standard-tier"></a>표준 계층으로 업그레이드
기본 계층에 있고 이러한 [제한](spring-cloud-quotas.md) 중 하나 이상에 의해 제한되는 경우 표준 계층으로 업그레이드할 수 있습니다. 이렇게 하려면 먼저 표준 계층 열을 선택하고 **업그레이드** 단추를 클릭하여 가격 책정 계층 메뉴로 이동합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Spring Cloud 애플리케이션의 크기를 수동으로 조정하는 방법에 대해 알아보았습니다. 경고를 설정하여 애플리케이션을 모니터링하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [경고를 설정하는 방법 알아보기](spring-cloud-tutorial-alerts-action-groups.md)
