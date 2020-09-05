---
title: Microsoft Azure FXT Edge 필터 unit을 종료 하는 방법
description: 클러스터 제어판 소프트웨어를 사용 하 여 Azure FXT Edge 필터 노드의 시작 및 안전 종료 절차를 알아봅니다.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: d597d5d1e86a2ea13363bc270fb55304d77dee5c
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184876"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge 필터 하드웨어를 안전 하 게 활용 하는 방법

실제 전원 단추를 사용 하 여 개별 노드를 전환할 수 있지만 일반적인 상황에서 단위를 종료 하는 데 사용 하면 안 됩니다.

Azure FXT Edge 필터 노드를 클러스터의 일부로 사용 하는 경우 클러스터 제어판 소프트웨어를 사용 하 여 하드웨어를 종료 해야 합니다. 

> [!NOTE] 
> 데이터 손실이 나 손상을 방지 하려면 항상 제어판 소프트웨어를 사용 하 여 Azure FXT Edge 필터를 종료 합니다. Microsoft 고객 서비스 및 지원 서비스에서이 작업을 수행 하도록 지시 하지 않는 한 종료 하기 위해 물리적 전원 단추를 사용 하지 마세요.
> 
> 전기 비상에서 전원 코드를 분리 하거나 데이터 센터의 전기 연결 끊기 메커니즘을 사용 합니다.

## <a name="shut-down-a-node-from-the-control-panel"></a>제어판에서 노드 종료

다음 지침에 따라 Azure FXT Edge 필터 노드의 전원을 안전 하 게 끕니다.

1. 클러스터 제어판에 로그인 합니다. ( [설정 페이지 열기](fxt-cluster-create.md#open-the-settings-pages)가이드)
1. **설정** 탭을 클릭 한 다음 **클러스터**  >  **fxt 노드** 페이지를 로드 합니다.
1. 클러스터 노드 목록에서 종료 하려는 클러스터 노드를 찾습니다. **작업** 열에서 **전원 아래로** 단추를 클릭 합니다. 
1. 몇 분 정도 기다립니다. 노드가 종료 되 고 전원이 꺼집니다.

## <a name="next-steps"></a>다음 단계

* [Azure FXT Edge 필터 하드웨어 상태 모니터](fxt-monitor.md)의 상태 led 및 기타 표시기에 대해 알아봅니다.
* [연결 전원 케이블](fxt-network-power.md#connect-power-cables)의 AZURE Fxt Edge 필터 전원 공급 장치에 대해 자세히 알아보세요.
