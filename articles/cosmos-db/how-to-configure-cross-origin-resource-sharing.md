---
title: Azure Cosmos DB에서 CORS(크로스-원본 자원 공유)
description: 이 문서에서는 Azure Portal 및 Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB에서 CORS(크로스-원본 자원 공유)를 구성하는 방법을 설명합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082984"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>CORS(원본 간 리소스 공유) 구성

CORS(크로스 원본 자원 공유)는 특정 도메인에서 실행되는 웹 애플리케이션이 다른 도메인의 자원에 액세스할 수 있도록 하는 HTTP 기능입니다. 웹 브라우저는 웹 페이지가 다른 도메인의 API를 호출하지 못하게 차단하는 동일 원본 정책이라고 하는 보안 제한을 구현합니다. 그러나 CORS는 원본 도메인이 다른 도메인의 Api를 호출할 수 있는 안전한 방법을 제공 합니다. Azure Cosmos DB의 코어 (SQL) API는 이제 "allowedOrigins" 헤더를 사용 하 여 CORS (원본 간 리소스 공유)를 지원 합니다. Azure Cosmos 계정에 CORS 지원을 사용하도록 설정하면 오직 인증된 요청만 평가하여 사용자가 지정한 규칙에 따라 허용 여부가 결정됩니다.

Azure Portal 또는 Azure Resource Manager 템플릿에서 CORS(크로스-원본 자원 공유) 설정을 구성할 수 있습니다. Core (SQL) API를 사용 하는 Cosmos 계정의 경우, Azure Cosmos DB node.js 및 브라우저 기반 환경 모두에서 작동 하는 JavaScript 라이브러리를 지원 합니다. 이 라이브러리는 게이트웨이 모드를 사용할 때 CORS 지원을 활용할 수 있습니다. 이 기능을 사용하기 위해 필요한 클라이언트 쪽 구성은 없습니다. CORS 지원을 사용하면 브라우저의 리소스가 [JavaScript 라이브러리](https://www.npmjs.com/package/@azure/cosmos)를 통해 또는 [REST API](https://docs.microsoft.com/rest/api/cosmos-db/)에서 직접 Azure Cosmos DB에 액세스하여 간단한 작업을 수행할 수 있습니다.

> [!NOTE]
> CORS 지원은 Azure Cosmos DB Core (SQL) API에만 적용 되 고 지원 됩니다. 이러한 프로토콜은 클라이언트-서버 통신에 HTTP를 사용 하지 않으므로 Cassandra, Gremlin 또는 MongoDB에 대한 Azure Cosmos DB Api에는 적용 되지 않습니다.

## <a name="enable-cors-support-from-azure-portal"></a>Azure Portal에서 CORS 지원을 사용하도록 설정

다음 단계에 따라 Azure Portal을 사용하여 크로스-원본 자원 공유를 사용하도록 설정하세요.

1. Azure cosmos DB 계정으로 이동합니다. **CORS** 블레이드를 엽니다.

2. Cosmos DB 계정에 대한 원본 간 호출을 만들 수 있는 쉼표로 구분된 원본 목록을 지정합니다. 예: `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. 와일드카드 "\*"를 사용하여 모든 원본을 허용하고 **제출**을 선택할 수도 있습니다. 

   > [!NOTE]
   > 현재 도메인 이름에는 와일드카드를 사용할 수 없습니다. 예를 들어 `https://*.mydomain.net` 형식은 아직 지원되지 않습니다. 

   ![Azure Portal을 사용하여 원본 간 리소스 공유를 사용하도록 설정](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>Resource Manager 템플릿에서 CORS 지원을 사용하도록 설정

Resource Manager 템플릿을 사용하여 CORS를 사용하도록 설정하려면 기존 템플릿에 속성이 "allowedOrigins"인 "cors" 섹션을 추가합니다. 다음 JSON은 CORS를 사용하도록 설정된 새 Azure Cosmos 계정을 만드는 템플릿의 예입니다.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>브라우저에서 Azure Cosmos DB JavaScript 라이브러리 사용

현재 Azure Cosmos DB JavaScript 라이브러리에는 패키지와 함께 제공되는 CommonJS 라이브러리 버전만 있습니다. 브라우저에서 이 라이브러리를 사용하려면 Rollup 또는 Webpack 같은 도구를 사용하여 브라우저 호환 라이브러리를 만들어야 합니다. 특정 Node.js 라이브러리는 브라우저 모의 개체가 있어야 합니다. 다음은 필요한 모의 설정이 들어 있는 Webpack 구성 파일의 예입니다.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
다음은 Azure Cosmos DB JavaScript SDK 라이브러리에 TypeScript 및 Webpack을 사용하여 새 항목이 생성되면 실시간 업데이트를 보내는 Todo 앱을 빌드하는 [코드 샘플](https://github.com/christopheranderson/cosmos-browser-sample)입니다.
모범 사례에 따라, 브라우저에서 Azure Cosmos DB와 통신하는 데 기본 키를 사용하지 마세요. 그 대신 리소스 토큰을 사용하여 통신하세요. 리소스 토큰에 대한 자세한 내용은 [Azure Cosmos DB에 대한 액세스 보호](secure-access-to-data.md#resource-tokens) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정을 보호하는 또 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB용 방화벽 구성](how-to-configure-firewall.md) 문서

* [Azure Cosmos DB 계정에 대한 가상 네트워크 및 서브넷 기반 액세스 구성](how-to-configure-vnet-service-endpoint.md)
