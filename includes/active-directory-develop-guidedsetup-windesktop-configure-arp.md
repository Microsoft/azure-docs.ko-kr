---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/17/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 5c2bce5635dfe488c1725efdd2954ecd81cf8e79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300625"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 애플리케이션의 등록 정보 추가
이 단계에서는 프로젝트에 애플리케이션 ID를 추가해야 합니다.

1. `App.xaml.cs`를 열고 다음으로 `ClientId`를 바꿉니다.

    ```csharp
    private static string ClientId = "[Enter the application Id here]";
    ```

<!-- ms.date: 11/05/2018 -->