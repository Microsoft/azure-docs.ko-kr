---
title: Azure Cosmos DB(미리 보기)에 기본 제공되는 Jupyter Notebooks 지원 소개
description: Azure Cosmos DB에 기본 제공되는 Jupyter Notebooks 지원을 사용하여 대화형으로 쿼리를 실행하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588159"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Azure Cosmos DB에 기본 제공되는 Jupyter Notebooks 지원(미리 보기)

Jupyter Notebook은 라이브 코드, 수식, 시각화 및 내레이션 텍스트를 포함하는 문서를 만들고 공유할 수 있는 오픈 소스 웹 애플리케이션입니다. 

Azure Cosmos DB의 기본 제공 Jupyter Notebooks는 Azure Portal 및 Azure Cosmos DB 계정에 직접 통합되므로 간편하고 편리하게 사용할 수 있습니다. 개발자, 데이터 과학자, 엔지니어 및 분석가는 친숙한 Jupyter Notebooks 환경을 사용하여 데이터 탐색, 데이터 정리, 데이터 변환, 수치 시뮬레이션, 통계 모델링, 데이터 시각화 및 기계 학습을 수행할 수 있습니다.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Azure Cosmos DB의 Jupyter Notebooks 시각화":::

Azure Cosmos DB는 Core(SQL), Cassandra, Gremlin, Table 및 API for MongoDB를 비롯한 모든 API에 C# 및 Python Notebook을 모두 지원합니다. Notebook 내에서 손쉽게 Azure Cosmos DB 리소스를 만들고, 데이터를 업로드하고, Azure Cosmos DB의 데이터를 쿼리하고 시각화할 수 있게 해주는 기본 제공 명령 및 기능을 활용할 수 있습니다. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Azure Cosmos DB의 Jupyter Notebooks 지원":::

## <a name="benefits-of-jupyter-notebooks"></a>Jupyter Notebooks의 이점

Jupyter Notebooks는 원래 Python 및 R로 작성된 데이터 과학 애플리케이션용으로 개발되었지만 다음과 같은 다양한 종류의 프로젝트에 다양한 방식으로 사용될 수 있습니다.

**데이터 시각화:** Jupyter Notebooks를 사용하면 데이터 세트를 그래픽으로 렌더링하는 공유 Notebook의 형태로 데이터를 시각화할 수 있습니다. 시각화를 만들고, 공유 코드 및 데이터 세트에 대한 대화형 변경을 수행하고, 결과를 공유할 수 있습니다.

**코드 공유:** GitHub와 같은 서비스는 코드를 공유하는 방법을 제공하지만 대개 대화형이 아닙니다. Jupyter Notebook을 사용하면 코드를 살펴보고 실행할 수 있으며 결과를 Azure Portal에 직접 표시할 수 있습니다.

**코드와 실시간 상호 작용:** Jupyter Notebook의 코드는 동적입니다. 이를 편집하고 실시간으로 증분 업데이트를 실행할 수 있습니다. 또한 코드, 데모 또는 POC(개념 증명)의 입력 소스로 사용되는 사용자 정의 컨트롤(예: 슬라이더 또는 텍스트 입력 필드)도 포함할 수 있습니다.

**코드 샘플 및 데이터 탐색 결과 문서화:** 코드 조각이 있고 작동 방식을 단계별로 설명하고자 하는 경우 Jupyter Notebook에 포함할 수 있습니다. 문서화하면서 대화형 작업을 동시에 추가할 수 있습니다.

**Azure Cosmos DB에 대한 기본 제공 명령:** Azure Cosmos DB의 기본 제공 매직 명령을 사용하면 계정과 쉽게 상호 작용할 수 있습니다. %%upload 및 %%sql 같은 명령을 사용하여 데이터를 컨테이너에 업로드하고 [SQL API 구문](sql-query-getting-started.md)을 사용하여 쿼리할 수 있습니다. 추가 사용자 지정 코드를 작성할 필요가 없습니다.

**한 곳에 모두 있는 환경:** Jupyter Notebooks는 코드, 서식 있는 텍스트, 이미지, 비디오, 애니메이션, 수학 방정식, 플롯, 지도, 대화형 그림, 위젯 및 그래픽 사용자 인터페이스를 단일 문서로 결합합니다.

## <a name="components-of-a-jupyter-notebook"></a>Jupyter Notebook의 구성 요소

Jupyter Notebooks에는 여러 유형의 구성 요소가 포함될 수 있고, 각각은 개별 블록 또는 셀로 구성됩니다.

**텍스트 및 HTML:** HTML을 생성하기 위해 마크다운 구문에 주석이 달린 텍스트 또는 일반 텍스트를 언제든지 문서에 삽입할 수 있습니다. CSS 스타일링은 노트북을 생성하는 데 사용되는 템플릿에 추가되거나 인라인으로 포함될 수도 있습니다.

**코드 및 출력:** Jupyter Notebooks는 Python 및 C# 코드를 지원합니다. 실행한 코드의 결과는 코드 블록 바로 뒤에 나타나며, 코드 블록을 원하는 순서대로 여러 번 실행할 수 있습니다.

**시각화:** Matplotlib, Plotly, Bokeh 등과 같은 모듈을 사용하여 코드에서 그래픽과 차트를 생성할 수 있습니다. 출력과 유사하게 이러한 시각화는 시각화를 생성하는 코드 옆에 인라인으로 나타납니다. 출력과 유사하게 이러한 시각화는 시각화를 생성하는 코드 옆에 인라인으로 나타납니다.

**멀티미디어:** Jupyter Notebooks는 웹 기술에 기반하기 때문에 웹 페이지에서 지원되는 모든 유형의 멀티미디어를 표시할 수 있습니다. 노트북에 HTML 요소로 포함하거나 `IPython.display` 모듈을 사용하여 프로그래밍 방식으로 생성할 수 있습니다.

**데이터:** Azure Cosmos 컨테이너의 데이터 또는 쿼리 결과를 프로그래밍 방식으로 Jupyter Notebook에 가져올 수 있습니다. 기본 제공 매직 명령을 사용하여 Azure Cosmos DB에서 데이터를 업로드하거나 쿼리합니다. 

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에 기본 제공되는 Jupyter Notebooks를 시작하려면 다음 문서를 참조하세요.

* [Azure Cosmos 계정에서 Notebook 사용](enable-notebooks.md)
* [Python Notebook 기능 및 명령 사용](use-python-notebook-features-and-commands.md)
* [C# Notebook 기능 및 명령 사용](use-csharp-notebook-features-and-commands.md)
