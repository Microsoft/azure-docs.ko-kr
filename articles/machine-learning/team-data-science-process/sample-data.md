---
title: 서로 다른 Azure Storage 위치의 샘플 데이터 - Team Data Science Process
description: 작지만 대표적이며 보다 관리하기 쉬운 크기로 줄이기 위한 Azure Blob 컨테이너, SQL Server 및 Hive 테이블의 샘플 데이터
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 878a2a47dfaddf1d3399a1dbd98865332838e148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321748"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Azure Blob 컨테이너, SQL Server 및 Hive 테이블의 데이터 샘플링

다음 문서에서는 세 가지 Azure 위치 중 하나에 저장된 데이터를 샘플링하는 방법을 설명합니다.

* [**Azure BLOB 컨테이너 데이터**](sample-data-blob.md)는 프로그래밍 방식으로 다운로드한 다음 샘플 Python 코드로 샘플링함으로써 샘플링됩니다.
* [**SQL Server 데이터**](sample-data-sql-server.md)는 SQL 및 Python 프로그래밍 언어를 둘 다 사용하여 샘플링됩니다. 
* [**Hive 테이블 데이터**](sample-data-hive.md)는 Hive 쿼리를 사용하여 샘플링됩니다.

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](./index.yml)의 단계입니다.

**데이터를 샘플링하는 이유**

분석할 데이터 세트가 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 크기를 줄이면 데이터 해석, 탐색 및 기능 엔지니어링이 용이해질 수 있습니다. Cortana 분석 프로세스의 이 샘플링 역할은 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.