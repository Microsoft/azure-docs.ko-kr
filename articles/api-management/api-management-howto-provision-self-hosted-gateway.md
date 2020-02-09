---
title: Azure API Management에서 자체 호스팅 게이트웨이 프로 비전 Microsoft Docs
description: Azure API Management에서 자체 호스팅 게이트웨이를 프로 비전 하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075280"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Azure API Management에서 자체 호스팅 게이트웨이 프로 비전

Azure API Management 인스턴스에서 게이트웨이 리소스를 프로 비전 하는 것은 자체 호스팅 게이트웨이를 배포 하기 위한 필수 구성 요소입니다. 이 문서에서는 API Management에서 게이트웨이 리소스를 프로 비전 하는 단계를 안내 합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능은 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 개발자 및 프리미엄 계층 에서만 추가 비용 없이 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한 됩니다.

## <a name="prerequisites"></a>선행 조건

다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>자체 호스팅 게이트웨이 프로비전

1. **설정**에서 **게이트웨이** 를 선택 합니다.
2. **+ 추가**를 클릭합니다.
3. 게이트웨이의 **이름** 및 **지역을** 입력 합니다.
> [!TIP]
> **Region** 이 게이트웨이 리소스와 연결 될 게이트웨이 노드의 위치를 지정 합니다. 모든 Azure 리소스와 연결 된 유사한 속성과 의미상 동일 하지만 임의의 문자열 값을 할당할 수 있습니다.

4. 필요에 따라 게이트웨이 리소스에 대한 **설명을** 입력 합니다.
5. 필요에 따라 **api** 아래에서 **+** 를 선택 하 여 하나 이상의 api를이 게이트웨이 리소스와 연결 합니다.
> [!TIP]
> Api의 **설정** 탭에 있는 게이트웨이에서 api를 연결 하 고 제거할 수 있습니다.

> [!IMPORTANT]
> 기본적으로 기존 Api는 새 게이트웨이 리소스와 연결 되지 않습니다. 따라서 새 게이트웨이를 통해 호출 하려고 하면 `404 Resource Not Found` 응답이 발생 합니다.

6. **추가**를 클릭합니다.

이제 게이트웨이 리소스가 API Management 인스턴스에 프로 비전 되었습니다. 게이트웨이 배포를 계속할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대해 자세히 알아보려면 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md) 를 참조 하세요.
* [Kubernetes에 자체 호스팅 게이트웨이를 배포](api-management-howto-deploy-self-hosted-gateway-to-k8s.md) 하는 방법에 대한 자세한 정보
* [자체 호스팅 게이트웨이를 Docker에 배포](api-management-howto-deploy-self-hosted-gateway-to-docker.md) 하는 방법에 대한 자세한 정보
