---
title: PCI-DSS v3.2.1 청사진 샘플 개요
description: Payment Card Industry Data Security Standard v3.2.1 청사진 샘플에 대한 개요입니다. 이 청사진 샘플은 고객이 특정 컨트롤을 평가하는 데 도움이 됩니다.
ms.date: 08/19/2020
ms.topic: sample
ms.openlocfilehash: 8835e3371c6df2b113143b615dd31b1c69261bc9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649179"
---
# <a name="overview-of-the-pci-dss-v321-blueprint-sample"></a>PCI DSS v3.2.1 청사진 샘플 개요

PCI-SS v3.2.1 청사진 샘플은 PCI-SS v3.2.1 규정 준수를 지원하는 일련의 정책입니다. 이 청사진은 고객이 PCI-DSS 워크로드를 통해 클라우드 기반 환경을 제어할 수 있도록 지원합니다.
PCI-SS 청사진은 이 인증이 필요한 Azure 배포 아키텍처에 일련의 핵심 정책을 배포합니다.

## <a name="control-mapping"></a>컨트롤 매핑

컨트롤 매핑 섹션에서는 이 이니셔티브에 포함된 정책에 대한 세부 정보와 이러한 정책이PCI-DSS v3.2.1에서 정의된 다양한 컨트롤을 어떻게 충족하는지를 설명합니다. 아키텍처에 할당된 리소스는 할당된 정책을 준수하지 않는지 Azure 정책에서 평가됩니다.

이 청사진을 할당한 후 Azure Policy 규정 준수 대시보드에서 Azure 환경 규정 준수 수준을 확인합니다.

## <a name="next-steps"></a>다음 단계

PCI DSS v3.2.1 청사진 샘플 개요를 살펴봤습니다. 이제 다음 문서를 참조하여 컨트롤 매핑 및 이 샘플을 배포하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [PCI DSS v3.2.1 청사진 - 컨트롤 매핑](./control-mapping.md)
> [PCI DSS v3.2.1 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기