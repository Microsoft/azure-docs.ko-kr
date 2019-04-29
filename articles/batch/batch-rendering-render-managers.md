---
title: 렌더링 관리자 지원 - Azure Batch
description: Azure Batch 렌더링 관리자 통합을 통해 렌더링에 Azure 사용
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bcc66a73e3d7986b177b13eb309ad664a006b960
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118589"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>렌더링 팜 관리자로 Azure Batch 사용

기존 온-프레미스 렌더링 팜을 사용할 경우 렌더링 관리자가 렌더링 팜 용량 및 렌더링 작업을 제어할 가능성이 큽니다.

Azure는 주요 렌더링 관리자에 대한 기본 제공 지원 또는 추가 기능을 제공합니다. 이에 따라 사용자가 종량제 애플리케이션 라이선스가 적용되는 VM 및 낮은 우선 순위 VM 등, Azure VM을 추가하고 제거할 수 있습니다.

다음 렌더링 관리자가 지원됩니다.

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Azure에 PipelineFX Qube 사용

Azure Batch 풀 VM을 Qube 작업자로 사용하도록 활성화하기 위한 스크립트와 명령은 [GitHub 리포지토리](https://github.com/Azure/azure-qube)에 있습니다.

## <a name="using-azure-with-royal-render"></a>Azure에 Royal Render 사용

Royal Render는 Azure와 Azure Batch 통합을 기본 제공하므로 Azure 기반 VM을 통해 렌더링 팜을 확장할 수 있습니다. 요약은 [도움말 파일](http://www.royalrender.de/help8/index.html?Cloudrendering.html)을 참조하세요.

Azure 통합을 사용하는 Royal Render 고객 예는 [Jellyfish Pictures 고객 스토리](https://customers.microsoft.com/story/jellyfishpictures)를 참조하세요.

## <a name="using-azure-with-thinkbox-deadline"></a>Azure에 Thinkbox Deadline 사용

Azure Batch 풀 VM을 Deadline 슬레이브로 사용하도록 활성화하기 위한 스크립트와 명령은 [GitHub 리포지토리](https://github.com/Azure/azure-deadline)에 있습니다.

## <a name="next-steps"></a>다음 단계

해당하는 경우 GitHub에서 적합한 플러그 인과 지침을 통해 렌더링 관리자에 Azure Batch 통합을 사용해 봅니다.