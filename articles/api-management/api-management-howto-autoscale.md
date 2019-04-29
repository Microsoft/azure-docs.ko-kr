---
title: Azure API Management 인스턴스의 자동 크기 조정 구성 | Microsoft Docs
description: 이 항목에서는 Azure API Management 인스턴스의 자동 크기 조정 동작을 설정하는 방법을 설명합니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
origin.date: 06/20/2018
ms.date: 12/31/2018
ms.author: v-yiso
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123470"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Azure API Management 인스턴스 자동 크기 조정  

Azure API Management 서비스 인스턴스가 규칙 집합을 기반으로 자동으로 크기를 조정할 수 있습니다. 이 동작은 Azure Monitor를 통해 사용 및 구성될 수 있으며 Azure API Management 서비스의 **표준** 및 **프리미엄** 계층에서만 지원됩니다.

이 문서는 자동 크기 조정을 구성하는 과정을 안내하고, 최적의 자동 크기 조정 규칙 구성을 제안합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 따르려면 다음이 필요합니다.

+ 활성 Azure 구독
+ Azure API Management 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
+ [Azure API Management 인스턴스의 용량](api-management-capacity.md)에 대한 개념을 이해합니다.
+ 비용이 소요되는 결과를 비롯하여 [Azure API Management의 수동 크기 조정 과정](upgrade-and-scale.md)을 이해합니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Azure API Management 자동 크기 조정 제한 사항

자동 크기 조정 동작을 구성하기 전에 크기 조정 결정에 대한 특정 제한 사항과 결과를 고려해야 합니다.

+ 자동 크기 조정은 Azure API Management 서비스의 **표준** 및 **프리미엄** 계층에서만 사용할 수 있습니다.
+ 가격 책정 계층에서도 서비스 인스턴스의 최대 단위 수를 지정합니다.
+ 크기 조정 과정에는 20분 이상 소요됩니다.
+ 다른 작업에 의해 서비스가 잠긴 경우 크기 조정 요청이 실패하고 자동으로 다시 시도합니다.
+ 다중 지역 배포를 사용하는 서비스의 경우 **기본 위치**의 단위만 크기 조정할 수 있습니다. 다른 위치의 단위는 크기 조정할 수 없습니다.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Azure API Management 서비스에 대한 자동 크기 조정 사용 및 구성

아래 단계에 따라 Azure API Management 서비스에 대한 자동 크기 조정을 구성합니다.

1. Azure Portal에서 **모니터** 인스턴스로 이동합니다.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. 왼쪽 메뉴에서 **자동 크기 조정**을 선택합니다.

    ![Azure Monitor 리소스 자동 크기 조정](media/api-management-howto-autoscale/02.png)

3. 드롭다운 메뉴에서 필터에 따라 Azure API Management 서비스를 찾습니다.
4. 원하는 API Management 서비스 인스턴스를 선택합니다.
5. 새로 열린 섹션에서 **자동 크기 조정 사용** 단추를 클릭합니다.

    ![Azure Monitor 자동 크기 조정 사용](media/api-management-howto-autoscale/03.png)

6. **규칙** 섹션에서 **+ 규칙 추가**를 클릭합니다.

    ![Azure Monitor 자동 크기 조정 규칙 추가](media/api-management-howto-autoscale/04.png)

7. 새 규모 확장 규칙을 정의합니다.

   예를 들어 지난 30분 동안의 평균 용량 메트릭이 80%를 초과하면 규모 확장 규칙에 따라 Azure API Management 단위 추가가 트리거될 수 있습니다. 아래 표에서 이러한 규칙에 대한 구성을 제공합니다.

    | 매개 변수             | 값             | 메모                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 메트릭 원본         | 현재 리소스  | 현재 Azure API Management 리소스 메트릭을 기반으로 규칙을 정의합니다.                                                                                                                                                                                                     |
    | *조건*            |                   |                                                                                                                                                                                                                                                                                 |
    | 시간 집계      | 평균           |                                                                                                                                                                                                                                                                                 |
    | 메트릭 이름           | 용량          | 용량 메트릭은 Azure API Management 인스턴스의 리소스 사용을 반영하는 Azure API Management 메트릭입니다.                                                                                                                                                            |
    | 시간 조직 통계  | 평균           |                                                                                                                                                                                                                                                                                 |
    | 연산자              | 초과      |                                                                                                                                                                                                                                                                                 |
    | 임계값             | 80%               | 평균 용량 메트릭에 대한 임계값입니다.                                                                                                                                                                                                                                 |
    | 기간(분) | 30                | 용량 메트릭의 평균을 구하는 시간 간격은 사용 패턴에 따라 다릅니다. 시간이 길어질수록 반응은 더 원활해지며 일시적인 스파이크는 스케일 아웃 결정에 적은 영향을 미칩니다. 그러나 스케일 아웃 트리거는 지연됩니다. |
    | *작업*              |                   |                                                                                                                                                                                                                                                                                 |
    | 작업(Operation)             | 다음을 기준으로 개수 늘이기 |                                                                                                                                                                                                                                                                                 |
    | 인스턴트 수        | 1                 | 1단위로 Azure API Management 인스턴스를 규모 확장합니다.                                                                                                                                                                                                                          |
    | 정지(분)   | 60                | Azure API Management 서비스를 규모 확장하는 데 20분 이상 소요됩니다. 대부분의 경우 60분의 정지 기간은 많은 스케일 아웃이 트리거되지 않도록 합니다.                                                                                                  |

8. **추가** 를 클릭하여 규칙을 저장합니다.

    ![Azure Monitor 규모 확장 규칙](media/api-management-howto-autoscale/05.png)

9. **+ 규칙 추가**를 다시 클릭합니다.

    이번에는 규모 감축 규칙을 정의해야 합니다. 이렇게 하면 API 사용량이 감소할 때 리소스를 낭비하지 않게 됩니다.

10. 새 규모 감축 규칙을 정의합니다.

    예를 들어 지난 30분 동안의 평균 용량 메트릭이 35%보다 낮으면 규모 감축 규칙에 따라 Azure API Management 단위 제거가 트리거될 수 있습니다. 아래 표에서 이러한 규칙에 대한 구성을 제공합니다.

    | 매개 변수             | 값             | 메모                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 메트릭 원본         | 현재 리소스  | 현재 Azure API Management 리소스 메트릭을 기반으로 규칙을 정의합니다.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *조건*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 시간 집계      | 평균           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 메트릭 이름           | 용량          | 규모 확장 규칙에 사용한 것과 동일한 메트릭                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | 시간 조직 통계  | 평균           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 연산자              | 다음보다 적음         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 임계값             | 35%               | 규모 확장 규칙과 마찬가지로 이 값은 Azure API Management의 사용 패턴에 크게 의존합니다. |
    | 기간(분) | 30                | 규모 확장 규칙에 사용한 것과 동일한 값                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *작업*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 작업(Operation)             | 다음을 기준으로 개수 줄이기 | 규모 확장 규칙에 사용한 것과 반대입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | 인스턴트 수        | 1                 | 규모 확장 규칙에 사용한 것과 동일한 값                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | 정지(분)   | 90                | 규모 감축은 규모 확장보다 더 보수적이므로 정지 기간이 더 길어야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. **추가** 를 클릭하여 규칙을 저장합니다.

    ![Azure Monitor 규모 감축 규칙](media/api-management-howto-autoscale/06.png)

12. Azure API Management 단위의 **최대** 수를 설정합니다.

    > [!NOTE]
    > Azure API Management에는 인스턴스 규모를 확장할 수 있는 단위 제한이 있습니다. 이 제한은 서비스 계층에 따라 달라집니다.

    ![Azure Monitor 규모 감축 규칙](media/api-management-howto-autoscale/07.png)

13. **저장**을 클릭합니다. 자동 크기 조정이 구성되었습니다.

## <a name="next-steps"></a>다음 단계

+ [여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법](api-management-howto-deploy-multi-region.md)
