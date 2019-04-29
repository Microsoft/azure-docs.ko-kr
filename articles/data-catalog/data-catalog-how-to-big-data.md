---
title: Azure Data Catalog에서 '빅 데이터' 데이터 원본으로 작업하는 방법
description: 방법 문서는 Azure Blob Storage, Azure Data Lake 및 Hadoop HDFS 등 '빅 데이터' 데이터 원본과 함께 Azure Data Catalog를 사용하기 위한 패턴을 강조 표시합니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b6b419d575e2164fc683b8e6b5020572db74d1b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001744"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Azure 데이터 카탈로그의 빅 데이터 소스로 작업하는 방법
## <a name="introduction"></a>소개
**Microsoft Azure 데이터 카탈로그**는 등록 시스템 및 기업 데이터 원본을 위한 검색 시스템 역할을 하는 완전히 관리되는 클라우드 서비스입니다. 데이터 카탈로그는 사람들이 데이터 원본을 검색하고 이해하고 사용하도록 도우면서 빅 데이터를 포함하여 조직의 기존 데이터 원본으로부터 더 많은 가치를 얻어내도록 돕는 역할을 합니다.

**Azure 데이터 카탈로그**는 Azure 블로그 저장소 BLOB 및 디렉터리뿐만 아니라 Hadoop HDFS 파일 및 디렉터리의 등록을 지원합니다. 이러한 데이터 원본의 반 구조화된 특성은 뛰어난 유연성을 제공합니다. 그러나 **Azure Data Catalog**를 사용하여 등록에서 가치를 극대화하려면 사용자는 데이터 원본이 구성되는 방법을 고려해야 합니다.

## <a name="directories-as-logical-data-sets"></a>논리적 데이터 집합으로 디렉터리 처리
빅 데이터 소스를 구성하기 위한 일반적인 패턴은 논리 데이터 집합으로 디렉터리를 처리하는 것입니다. 하위 폴더가 파티션을 정의하는 동안 최상위 디렉터리는 데이터 집합을 정의하는 데 사용되며, 포함된 파일은 데이터를 저장합니다.

이 패턴의 예는 다음과 같습니다.

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

이 예에서는 vehicle_maintenance_events 및 location_tracking_events가 논리 데이터 집합을 나타냅니다. 이러한 폴더 각각에는 년 및 월 단위로 하위 폴더에 정리된 데이터 파일이 있습니다. 이러한 폴더 각각에는 수백 또는 수천 개의 파일이 있을 수 있습니다.

이 패턴에서 개별 파일을 **Azure 데이터 카탈로그**에 등록해도 이를 사용할 수 없습니다. 대신 데이터로 작업하는 사용자에게 의미 있는 데이터 집합을 나타내는 디렉터리를 등록합니다.

## <a name="reference-data-files"></a>참조 데이터 파일
보완 패턴은 참조 데이터 집합을 개별 파일로 저장하는 것입니다. 이러한 데이터 집합은 빅 데이터의 '작은' 부분으로 생각할 수 있으며 분석 데이터 모델의 차원과 비슷한 경우가 많습니다. 참조 데이터 파일에는 빅 데이터 저장소에 저장된 대량의 데이터 파일에 대한 컨텍스트를 제공하는 데 사용되는 레코드가 있습니다.

이 패턴의 예는 다음과 같습니다.

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

분석가나 데이터 과학자가 보다 큰 디렉터리 구조에 포함된 데이터로 작업하는 경우, 이러한 참조 파일의 데이터는 보다 큰 데이터 집합에서 이름 또는 ID로만 참조되는 엔터티에 대해 보다 자세한 정보를 제공하는 데 사용할 수 있습니다.

이 패턴에서 개별 참조 데이터 파일을 **Azure Data Catalog**에 등록할 때 의미가 있습니다. 각 파일은 데이터 집합을 나타내며, 각각에 대해 개별적으로 주석을 추가하고 검색할 수 있습니다.

## <a name="alternate-patterns"></a>대체 패턴
이전 섹션에서 설명한 패턴은 빅 데이터 저장소를 정리하는 두 가지 방법이 있지만, 각 구현 방식은 달라집니다. 데이터 소스의 구조화는 방식과 빅 데이터 소스를 **Azure Data Catalog**에 등록하는 시점에 관계 없이, 조직 내 다른 사람에게 가치가 있는 데이터 집합을 나타내는 파일 및 디렉터리를 등록하는 것에 집중합니다. 모든 파일 및 디렉터리를 등록하면 카탈로그를 복잡하게 만들어, 사용자가 필요한 정보를 찾기 어려워집니다.

## <a name="summary"></a>요약
데이터 소스를 **Azure 데이터 카탈로그**에 등록하면 보다 쉽게 검색하고 이해할 수 있습니다. 논리 데이터 집합을 나타내는 빅 데이터 파일 및 디렉터리를 등록하고 주석을 추가하면, 사용자는 필요한 빅 데이터 소스를 찾아 사용할 수 있습니다.
