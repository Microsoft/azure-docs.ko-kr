---
title: Azure Security Center에서 인시던트 및 경고 조사 | Microsoft Docs
description: 이 문서를 통해 Azure Security Center에서 조사 기능을 사용하여 보안 인시던트 및 경고를 조사할 수 있습니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 6ba21c6eacd5b72d13706c08f0cc9883ccc91388
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704840"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Azure Security Center에서 인시던트 및 경고 조사(미리 보기)
이 문서를 통해 Azure Security Center에서 조사 기능을 사용하여 보안 인시던트 및 경고를 조사할 수 있습니다.

## <a name="what-is-investigation-in-security-center"></a>Security Center의 조사란?
Security Center의 조사 기능을 사용하면 범위를 심사하고, 이해하고, 잠재적인 [보안 인시던트](https://docs.microsoft.com/azure/security-center/security-center-incident)의 근본 원인을 추적할 수 있습니다.

해당 기능은 조사 중인 인시던트와 관련된 모든 엔터티([보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), 사용자, 컴퓨터 및 인시던트)를 연결하여 조사 프로세스를 용이하게 하기 위해 사용합니다.  Security Center는 포함된 엔터티와 관련 데이터의 상관 관계를 지정하고 개체를 탐색하고 관련 정보를 시각화하는 데 도움이 되는 라이브 그래프를 사용하는 이 상관 관계를 노출하여 이를 수행할 수 있습니다.


> [!NOTE]
> * [사용자 지정 경고](security-center-custom-alert.md)는 Security Center의 조사 기능에서 지원되지 않습니다.
> * Windows 서버에서 수집 된 데이터를 기반으로 경고에 대 한 조사만 지원 됩니다.


## <a name="how-investigation-works"></a>조사 작동 방식
조사는 조사 대시보드의 중앙 영역을 차지하는 그래프에 의해 구성됩니다. 그래프는 항상 특정 엔터티에 집중하여 이에 관련된 엔터티를 나타냅니다. 엔터티는 보안 경고, 사용자, 컴퓨터 또는 인시던트일 수 있습니다.

![Map](./media/security-center-investigation/security-center-investigation-fig1.png)

사용자는 그래프에서 엔터티를 클릭하여 여러 엔터티를 탐색할 수 있습니다. 그래프는 선택된 엔터티 및 해당 관련 엔터티에 자동으로 중앙 집중합니다. 그래프에서 더 이상 관련 없는 엔터티를 제거할 수 있습니다.

### <a name="investigation-path"></a>조사 경로
사용자가 다른 엔터티를 탐색하는 동안 조사 경로를 통해 조사 컨텍스트를 추적하고 빠르게 탐색할 수 있습니다. 조사 결과를 포함하는 인시던트는 항상 조사 경로에서 맨 왼쪽의 인시던트에 있습니다.

![path](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>일반 정보
엔터티가 그래프에 표시되면 탭이 이 엔터티에 대한 추가 정보를 보여줍니다. **정보** 탭에서는 사용할 수 있는 다양한 정보 원본의 엔터티에 대한 일반 정보를 제공합니다.

![일반 정보](./media/security-center-investigation/security-center-investigation-fig3.png)

정보 탭에서는 지도에서 선택한 인시던트에 관련된 정보를 표시합니다. 인시던트는 조사의 결과를 포함하는 컨테이너입니다. 각 조사는 인시던트의 컨텍스트에서 발생합니다.

사용자가 특정 경고에 대한 **조사 시작** 단추를 클릭하는 경우 인시던트가 만들어집니다. 조사기에 사용할 수 있는 기본 기능은 사용자, 컴퓨터 또는 경고와 같은 엔터티를 표시하는 것입니다. 엔터티가 관련됨으로 표시되면 이유를 제공합니다. 이 지점에서부터 이 엔터티는 그래프의 인시던트 및 인시던트 엔터티 목록에서 바로 아래 나타납니다.

### <a name="entities"></a>엔터티

**엔터티** 탭에서는 유형별로 그룹화된 관련 엔터티를 모두 표시합니다. 너무 많은 엔터티가 그래프에 표시되는 경우 및 엔터티 이름이 너무 긴 경우에 유용합니다. 또한 표 형식으로 검사하는 것이 쉽습니다.

![엔터티](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>검색

**검색** 탭은 엔터티에 사용할 수 있는 모든 로그 형식을 표시합니다. 각 로그 형식에서 사용할 수 있는 레코드 수를 확인할 수 있습니다. 각 로그 형식을 클릭하면 검색 화면으로 이동합니다. 검색 화면에서 검색을 구체화하고 경고 설정 등 다양한 검색 기능을 사용할 수 있습니다. 현재 릴리스에서 검색 탭은 사용자 및 컴퓨터 엔터티에 대해서만 사용할 수 있습니다.

![검색](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>탐색

**탐색** 탭을 사용하면 조사기에서 엔터티와 관련된 다양한 문제와 관련된 데이터를 검사할 수 있습니다. 예를 들어 컴퓨터를 조사하는 경우 여기서 실행된 프로세스의 목록은 탐색 탭에 표시됩니다. 경우에 따라 탐색 탭은 의심 스러운 문제를 나타낼 수 있는 데이터를 표시합니다. 조사기는 탭 내에서 데이터를 검사하거나 검색 화면에서 열어서 큰 데이터 집합을 검사하고 필터링 및 Excel로 내보내기 등 고급 검색 옵션을 사용할 수 있습니다.

![탐색](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>타임라인

그래프에 표시되는 대부분의 데이터 및 다양한 탭은 특정 기간 동안 관련이 있습니다. 이 시간 범위는 그래프의 맨 위 왼쪽에서 시간 범위 선택기를 사용하도록 설정됩니다. 조사기에는 시간 범위를 선택할 수 있는 다양한 방법이 있습니다.

![타임라인](./media/security-center-investigation/security-center-investigation-fig7.png)

다음 항목은 시간 범위에 민감합니다.

- 그래프의 사용자 컴퓨터 관계에는 이 시간 범위 내의 컴퓨터에 로그인하는 사용자만이 표시됩니다.
- 컴퓨터와 사용자를 검사할 때 경고가 표시됩니다. 시간 범위 내에서 발생하는 경고만 표시됩니다.
- 엔터티 탭은 그래프와 동일한 논리를 따릅니다.

다음 항목은 선택한 시간 범위에 상관 없이 표시됩니다.

- 경고가 표시되면 사용자와 컴퓨터 등을 포함하는 모든 엔터티가 항상 표시됩니다.
- 인시던트가 엔터티를 포함하는 경우 표시됩니다.

> [!NOTE]
> **검색** 및 **탐색** 탭은 이 시간 범위 내에 있는 레코드만을 표시합니다.

## <a name="how-to-perform-an-investigation"></a>조사를 수행 하는 방법

보안 인시던트 또는 경고에서 조사를 시작할 수 있습니다. 선택한 옵션은 필요에 따라 달라집니다. 다음 단계는 경고에서 조사를 시작 하는 데 사용 됩니다.

1.  **Security Center** 대시보드를 엽니다.
2.  **보안 경고**를 클릭하여 조사하려는 인시던트를 선택합니다.
3.  인시던트의 페이지에서 **시작 조사** 단추를 클릭하면 **조사** 대시보드가 표시됩니다.

    ![경고](./media/security-center-investigation/security-center-investigation-fig8.png)

4. 이 대시보드에서 맵에 있는 엔터티를 선택할 수 있고 이 엔터티의 관련 정보는 화면 오른쪽에 나타납니다.

    ![조사 대시보드](./media/security-center-investigation/security-center-investigation-fig9.png)

이 지점에서부터 이 인시던트에 관련된 엔터티를 탐색하고 각각에 대한 자세한 정보를 탐색할 수 있습니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 Security Center에서 조사 기능을 사용하는 방법을 살펴보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
