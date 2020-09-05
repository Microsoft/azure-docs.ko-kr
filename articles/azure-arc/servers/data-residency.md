---
title: 데이터 상주
description: 데이터 상주 및 Azure Arc 사용 서버 (미리 보기)에 대 한 정보입니다.
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048859"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Azure Arc 사용 서버 (미리 보기): 데이터 상주

이 문서에서는 데이터 상주의 개념과 Azure Arc 사용 서버 (미리 보기)에 적용 되는 방식을 설명 합니다.

Azure Arc 사용 가능 서버 (미리 보기)는 **미국, 유럽 또는 아시아 태평양**의 **[미리 보기에서 사용할 수 있습니다](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** .

## <a name="data-residency"></a>데이터 상주

Azure Arc 사용 서버 (미리 보기)는 연결 된 컴퓨터에서을 사용 하도록 설정 하기 전에 확장을 지정 해야 하는 [AZURE VM 확장](manage-vm-extensions.md) 구성 설정 (즉, 속성 값)을 저장 합니다. 예를 들어 Log Analytics VM 확장을 사용 하도록 설정 하면 Log Analytics **작업 영역 ID** 및 **기본 키**를 요청 합니다.

연결 된 컴퓨터에 대 한 메타 데이터 정보도 수집 됩니다. 특히 다음에 대한 내용을 설명합니다.

* 운영 체제 이름 및 버전
* 컴퓨터 이름
* 컴퓨터의 FQDN (정규화 된 도메인 이름)
* 연결 된 컴퓨터 에이전트 버전

Arc 사용 서버 (미리 보기)를 사용 하면 데이터가 저장 될 지역을 지정할 수 있습니다. Microsoft는 데이터 복원 력을 위해 다른 지역에 복제 될 수 있지만, Microsoft는 지리 외부에서 데이터를 복제 하거나 이동 하지 않습니다. 이 데이터는 Azure Arc 컴퓨터 리소스가 구성 된 지역에 저장 됩니다. 예를 들어 컴퓨터가 미국 동부 지역의 호에 등록 된 경우이 데이터는 미국 지역에 저장 됩니다.

지역 복원 력 및 규정 준수 지원에 대 한 자세한 내용은 [Azure 지리](https://azure.microsoft.com/global-infrastructure/geographies/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure 복원 력](/azure/architecture/reliability/architect)설계에 대해 자세히 알아보세요.