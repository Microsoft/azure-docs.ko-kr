---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86230993"
---
모든 최신 세대 VM 크기는 호스트에서 암호화를 지원합니다.

|Type  |지원되지 않음  |지원됨  |
|---------|---------|---------|
|범용 가상 컴퓨터     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|컴퓨팅 최적화     |         | Fsv2        |
|메모리에 최적화     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Storage에 최적화     |         | Ls, Lsv2(NVMe 디스크는 암호화되지 않음)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2(미리 보기)        |
|고성능 컴퓨팅     | H        | HB, HC, HBv2        |
|이전 세대     | F, A, D, L, G        | DS, GS, Fs, NVv2        |

VM 크기를 업그레이드하면 새 VM 크기가 EncryptionAtHost 기능을 지원하는지 확인하기 위해 유효성 검사가 수행됩니다.
