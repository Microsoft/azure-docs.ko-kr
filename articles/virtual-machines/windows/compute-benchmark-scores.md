---
title: Azure Windows VM에 대한 벤치마크 점수 계산 | Microsoft Docs
description: Windows Server를 실행하는 Azure VM에 대한 SPECint 계산 벤치마크 점수를 비교합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 43d0ab6552847df7f1f2a8599dcc7cb9a8fcb57b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844077"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Windows VM에 대한 벤치마크 점수 계산
다음 SPECInt 벤치마크 점수는 Windows Server를 실행하는 Azure의 고성능 VM 라인업에 대한 계산 성능을 보여 줍니다. [Linux VM](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대해 Compute 벤치마크 점수를 사용할 수도 있습니다.

> [!NOTE]
> Linux 숫자는 최근에 업데이트되었으며 VM의 보다 포괄적인 집합을 포함합니다.

## <a name="a-series---compute-intensive"></a>A 시리즈-계산 집약적
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |19 |130.5 |4.4. |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G-시리즈, GS 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율 | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 \@ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>H 시리즈
| 크기 | vCPU | NUMA 노드 | CPU | 실행 | Avg 기본 요율  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |573.2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>SPECint 정보
Windows 번호는 Windows Server에서 [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) 을 실행하여 계산합니다. SPECint는 vCPU당 하나의 복사본과 함께 기본 요율 옵션(SPECint_rate2006)을 사용하여 실행합니다. SPECint은 12번의 별도 테스트로 각기 세 번 실행되도록 구성되며, 각 테스트에서 중간값을 취해 가중치를 주어 종합 점수를 형성합니다. 그런 다음 여러 VM에서 실행하여 평균값을 구합니다.

## <a name="next-steps"></a>다음 단계
* 저장 용량, 디스크 세부 정보 및 VM 크기 선택시 추가적인 고려 사항에 관한 자세한 내용은 [가상 머신의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

