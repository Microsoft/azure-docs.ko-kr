---
title: Azure Data Factory 데이터 흐름 매핑 개요
description: Azure Data Factory의 데이터 흐름 매핑에 대한 간략한 설명
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261965"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory의 데이터 흐름 매핑은 무엇인가요?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

데이터 흐름 매핑은 데이터 엔지니어가 코드를 작성하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있도록 합니다. 결과 데이터 흐름은 스케일 아웃 Azure Databricks 클러스터를 사용하여 Azure Data Factory 파이프라인 내 작업으로 실행됩니다.

Azure Data Factory 데이터 흐름의 목적은 코딩할 필요 없이 완전 시각적 환경을 제공하는 것입니다. 데이터 흐름은 스케일 아웃 데이터 처리에 대해 사용자의 고유한 실행 클러스터에서 실행됩니다. Azure Data Factory는 모든 코드 변환, 경로 최적화 및 데이터 흐름 작업의 실행을 처리합니다.

변환 논리를 대화형으로 확인할 수 있도록 디버그 모드에서 데이터 흐름을 만들면서 시작합니다. 다음으로, 데이터 흐름 작업을 파이프라인에 추가하여 파이프라인 디버그에서 데이터 흐름을 실행 및 테스트하거나, 파이프라인에서 “지금 트리거”을 사용하여 파이프라인 활동의 데이터 흐름을 테스트합니다.

그런 다음, 데이터 흐름 활동을 실행하는 Azure Data Factory 파이프라인을 사용하여 데이터 흐름 작업을 예약 및 모니터링합니다.

데이터 흐름 디자인 화면의 디버그 모드 설정/해제 스위치로 데이터 변환의 대화형 빌드가 가능합니다. 디버그 모드는 데이터 흐름 생성을 위한 데이터 준비 환경을 제공합니다.
