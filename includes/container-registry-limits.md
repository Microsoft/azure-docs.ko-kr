---
title: 포함 파일
description: 포함 파일
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 844e4a0d2715799b808f2c7630c201f6e792bd63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582815"
---
| 리소스 | Basic | 표준 | Premium |
|---|---|---|---|
| Storage<sup>1</sup> | 10GiB | 100GiB| 500GiB |
| 최대 이미지 계층 크기 | 20GiB | 20GiB | 50GiB |
| 분당 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10000 |
| 분당 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 다운로드 대역폭 MBps<sup>2</sup> | 30 | 60 | 100 |
| 업로드 대역폭 MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 지역에서 복제 | N/A | N/A | [지원됨][geo-replication] |
| 콘텐츠 신뢰(미리 보기) | N/A | N/A | [지원됨][content-trust] |

<sup>1</sup>지정 된 저장소 제한은 양을 *포함* 각 계층에 대 한 저장소입니다. 이러한 한도 초과 이미지 저장소에 대해 GiB당 추가 일별 요금이 청구됩니다. 환율 정보를 참조 하세요. [Azure Container Registry 가격 책정][pricing]합니다.

<sup>2</sup>*ReadOps*하십시오 *WriteOps*, 및 *대역폭* 은 최소 추정치입니다. Azure Container Registry 사용 현황에 필요에 따라 성능을 개선 하려고 합니다.

<sup>3</sup>A [docker 풀](https://docs.docker.com/registry/spec/api/#pulling-an-image) 이미지와 매니페스트 검색을 계층의 수에 따라 여러 읽기 작업으로 변환 합니다.

<sup>4</sup>A [docker 푸시](https://docs.docker.com/registry/spec/api/#pushing-an-image) 푸시 해야 하는 레이어 수에 따라 여러 쓰기 작업으로 변환 됩니다. `docker push`에는 기존 이미지에 대해 매니페스트를 검색할 *ReadOps*가 포함되어 있습니다.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
