---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88639206"
---
아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성해야 하는 경우 다음 두 인수를 사용합니다.

| 속성 | 데이터 형식 | Description |
|--|--|--|
|HTTPS_PROXY|문자열|사용할 프록시(예: `https://proxy:8888`)<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|문자열|프록시를 인증하는 데 필요한 자격 증명(예: username:password).|
|`<proxy-user>`|문자열|프록시 사용자입니다.|
|`<proxy-password>`|문자열|프록시에 대해 `<proxy-user>`에 연결된 암호입니다.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
