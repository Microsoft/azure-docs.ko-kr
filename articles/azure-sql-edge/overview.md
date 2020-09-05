---
title: Azure SQL Edge(미리 보기)란?
description: Azure SQL Edge(미리 보기)에 대해 알아보기
keywords: SQL Edge 소개, SQL Edge란, SQL Edge 개요
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2c96e4b7baa2c463c42db9440cadb3cb396fde1b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642472"
---
# <a name="what-is-azure-sql-edge-preview"></a>Azure SQL Edge(미리 보기)란?

Azure SQL Edge(미리 보기)는 IoT 및 IoT Edge 배포에 최적화된 관계형 데이터베이스 엔진으로 IoT 애플리케이션과 솔루션을 위한 고성능 데이터 스토리지 및 처리 계층을 만드는 기능을 제공합니다. Azure SQL Edge는 JSON, 그래프, 시계열 데이터 등의 관계형 및 비관계형 데이터를 스트리밍, 처리 및 분석하는 기능을 제공하므로 다양한 최신 IoT 애플리케이션에 적합한 선택입니다.

Azure SQL Edge는 업계 최고의 성능, 보안 및 쿼리 처리 기능을 제공 하는 최신 버전의 Microsoft SQL Database 엔진 (/sql/sql-server/sql-server-technical-documentation? toc =/azure/azure-sql-edge/toc.json)을 기반으로 합니다. Azure SQL Edge는 [SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/azure-sql-edge/toc.json) 및 [azure sql](https://docs.microsoft.com/azure/azure-sql/)과 동일한 엔진을 기반으로 하기 때문에 응용 프로그램 또는 솔루션 개발을 더 쉽고 빠르게 수행할 수 있도록 하는 동일한 t-sql 프로그래밍 노출 영역을 제공 하 고, 동시에 IoT Edge 장치, 데이터 센터 및 클라우드 간에 응용 프로그램 이식성을 제공 합니다.

> [!NOTE]
> Azure SQL Edge는 현재 미리 보기로 제공되므로 프로덕션 환경에서는 사용할 수 없습니다.

## <a name="deployment-models"></a>배포 모델

Azure SQL Edge는 Azure Marketplace에서 사용할 수 있으며 [Azure IoT Edge](../iot-edge/about-iot-edge.md)용 모듈로 배포할 수 있습니다. 자세한 내용은 [Azure SQL Edge 배포](deploy-portal.md)를 참조하세요.<br>

![SQL Edge 개요 다이어그램](media/overview/overview.png)

## <a name="editions-of-sql-edge"></a>SQL Edge 버전

SQL Edge는 두 가지 버전 또는 소프트웨어 플랜으로 제공됩니다. 세 버전의 기능 세트는 동일하고 사용 권한과 지원하는 cpu/메모리의 양만 다릅니다.

   |**계획**  |**설명**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  개발 전용 sku이며, 각 SQL Edge 컨테이너는 최대 4코어, 32GB 메모리로 제한됩니다.  |
   |Azure SQL Edge    |  프로덕션 sku이며, 각 SQL Edge 컨테이너는 최대 8코어, 64GB 메모리로 제한됩니다. |

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

Azure SQL Edge는 현재 미리 보기로 제공됩니다. 가격 책정 및 가용성에 대한 자세한 내용은 [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/)를 참조하세요.

> [!IMPORTANT]
> Azure SQL Edge와 SQL Server 간의 기능 차이점과 다양한 Azure SQL Database 옵션 간의 차이점을 알아보려면 [Azure SQL Edge에서 지원하는 기능](features.md)을 참조하세요.

## <a name="streaming-capabilities"></a>스트리밍 기능  

Azure SQL Edge는 실시간 분석 및 복잡한 이벤트 처리를 위한 스트리밍 기능을 기본적으로 제공합니다. 스트리밍 기능은 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)와 동일한 구문 및 [IoT Edge의 Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md)와 유사한 기능을 사용하여 빌드되었습니다.

Azure SQL Edge의 스트리밍 엔진은 짧은 대기 시간, 복원력, 대역폭의 효율적 사용 및 규정 준수를 위해 고안되었습니다. 

SQL Edge의 데이터 스트리밍에 대한 자세한 내용은 [데이터 스트리밍](stream-data.md)을 참조하세요.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning 및 AI 기능

Azure SQL Edge는 개방형 형식 ONNX(Open Neural Network Exchange) 런타임을 통합하여 기본 제공 기계 학습 및 분석 기능을 제공하므로, 여러 프레임워크 간에 딥 러닝 및 신경망 모델을 교환할 수 있습니다. ONNX에 대한 자세한 내용은 [여기](https://onnx.ai/)를 참조하세요. ONNX 런타임은 원하는 언어 또는 도구로 모델을 개발한 다음, SQL Edge 내에서 실행 가능하도록 ONNX 형식으로 변환할 수 있는 유연성을 제공합니다. 자세한 내용은 [SQL Edge에서 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)를 참조하세요.

## <a name="working-with-azure-sql-edge"></a>Azure SQL Edge 작업

Azure SQL Edge를 사용하면 애플리케이션을 더 쉽고 효율적으로 개발하고 유지 관리할 수 있습니다. 사용자는 친숙한 도구와 기술을 사용하여 IoT Edge 요구 사항에 맞는 멋진 앱과 솔루션을 빌드할 수 있습니다. 사용자는 SQL Edge에서 다음과 같은 도구를 사용하여 개발할 수 있습니다.

- [Azure Portal](https://portal.azure.com/): 모든 Azure 서비스를 관리하는 웹 기반 애플리케이션입니다.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/): SQL Server에서 SQL Database에 이르는 모든 SQL 인프라를 관리하는 체험 다운로드 가능한 클라이언트 애플리케이션입니다.
- [Visual Studio의 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/): SQL Server 관계형 데이터베이스, SQL 데이터베이스, Integration Services 패키지, Analysis Services 데이터 모델 및 Reporting Services 보고서를 개발하는 다운로드 가능한 무료 클라이언트 애플리케이션입니다.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Windows, macOS 및 Linux에서 Microsoft 온-프레미스 및 클라우드 데이터 플랫폼 제품군을 사용하는 데이터 전문가를 위한 플랫폼 간 데이터베이스 도구이며 무료로 다운로드할 수 있습니다.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Windows, macOS 및 Linux용 오픈 소스 코드 편집기이며 무료로 다운로드할 수 있습니다. Microsoft SQL Server, Azure SQL Database 및 Azure SQL Data Warehouse를 쿼리하는 [mssql 확장](https://aka.ms/mssql-marketplace)을 비롯한 여러 확장을 지원합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Portal를 통해 SQL Edge 배포](deploy-portal.md)
- [SQL Edge를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md)
