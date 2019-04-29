---
title: Azure Storage 작업을 위한 도구 | Microsoft Docs
description: Azure Storage 데이터를 보고 상호 작용할 수 있는 도구 목록입니다.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.subservice: common
ms.openlocfilehash: ae6ceee33aa6d96c360782c33232e0c7561cf209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483114"
---
# <a name="azure-storage-client-tools"></a>Azure Storage 클라이언트 도구
Azure Storage의 사용자는 Azure Storage 클라이언트 도구를 사용하여 데이터를 보기/상호 작용하려는 경우가 많습니다. 아래 표에는 이 작업을 수행할 수 있게 해 주는 여러 도구가 나와 있습니다. 해당 도구가 데이터 추상화를 열거 및/또는 액세스할 수 있는 기능을 제공하는 경우 각 블록에 “X” 표시를 합니다. 또한 도구가 무료인지 여부도 나와 있습니다. “평가판”은 무료 평가판이 있음을 나타내지만 정품은 무료가 아님을 나타냅니다. “Y/N”는 특정 버전은 무료로 제공되지만 다른 버전은 구매할 수 있는지를 나타냅니다.

사용 가능한 Azure Storage 클라이언트 도구의 스냅숏만 제공합니다. 이러한 도구의 기능은 계속 개선 및 확장될 수 있습니다. 수정 사항이나 업데이트가 있는 경우 의견을 남겨주세요. 여기에 포함해야 할 도구가 있는 경우에도 알려주시면 추가해 드리겠습니다.

**Microsoft Azure Storage 클라이언트 도구**

<table>
  <tr>
    <th rowspan="2">Azure Storage 클라이언트 도구</th>
    <th rowspan="2">블록 Blob</th>
    <th rowspan="2">페이지 Blob</th>
    <th rowspan="2">Blob 추가</th>
    <th rowspan="2">테이블</th>
    <th rowspan="2">큐</th>
    <th rowspan="2">파일</th>
    <th rowspan="2">무료</th>
    <th colspan="4">플랫폼</th>
  </tr>
  <tr>
    <td>웹</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio 서버 탐색기</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**타사 Azure Storage 클라이언트 도구**

다음의 타사 도구가 주장하는 기능이나 품질은 보장할 수 없으며 목록에 포함되었다고 해서 Microsoft가 보증한다는 것을 의미하지 않습니다.

<table>
  <tr>
    <th rowspan="2">Azure Storage 클라이언트 도구</th>
    <th rowspan="2">블록 Blob</th>
    <th rowspan="2">페이지 Blob</th>
    <th rowspan="2">Blob 추가</th>
    <th rowspan="2">테이블</th>
    <th rowspan="2">큐</th>
    <th rowspan="2">파일</th>
    <th rowspan="2">무료</th>
    <th colspan="4">플랫폼</th>
  </tr>
  <tr>
    <td>웹</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>평가판</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>Y/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>평가판</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>평가판</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
