---
title: 요약 - Microsoft Azure의 컨테이너 보안
description: Microsoft Azure의 컨테이너 보안에 대한 요약 백서.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590090"
---
# <a name="container-security-in-microsoft-azure"></a>Microsoft Azure의 컨테이너 보안
## <a name="abstract"></a>요약

컨테이너 기술은 클라우드 컴퓨팅 환경에서 구조적인 변경을 야기합니다. 컨테이너를 통해 운영 체제의 단일 인스턴스에서 애플리케이션의 여러 인스턴스를 실행할 수 있으므로 리소스를 더 효율적으로 사용할 수 있습니다. 컨테이너는 조직 일관성 및 유연성을 제공합니다. 애플리케이션은 데스크톱에서 개발되고 가상 머신에서 테스트된 다음, 클라우드에서 프로덕션용으로 배포될 수 있으므로 컨테이너로 지속적인 배포가 가능합니다. 컨테이너는 리소스 최적화로 인해 민첩성, 능률적인 작업, 확장성 및 비용 절감을 제공합니다.

컨테이너 기술은 비교적 새로운 분야이기 때문에 많은 IT 전문가가 프로덕션 환경에서의 가시성 및 사용에 대해 보안 문제를 갖고 있습니다. 개발 팀은 종종 보안 모범 사례를 인식하지 않습니다. 이 백서는 Microsoft Azure 플랫폼에서 컨테이너 개발 및 배포를 보호하는 방법을 선택하는 보안 운영 팀 및 개발자에게 도움이 될 수 있습니다.

이 문서에서는 컨테이너, 컨테이너 배포 및 관리, 네이티브 플랫폼 서비스를 설명합니다. 또한 Azure 플랫폼에서 컨테이너 사용과 관련하여 발생하는 런타임 보안 문제를 설명합니다. 그림 및 예제에서 이 문서는 컨테이너 모델로 Docker에, 컨테이너 오케스트레이터로 Kubernetes에 집중합니다. 대부분의 보안 권장 사항은 Azure 플랫폼에서 Microsoft 파트너의 다른 컨테이너 모델에도 적용됩니다.

[백서 다운로드](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)