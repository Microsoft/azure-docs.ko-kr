---
title: Azure FileUpload UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Common.FileUpload UI 요소에 대해 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 92a5f7c058904015cb22a239b7e7c4938ae1fdae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044655"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI 요소
사용자가 업로드할 파일을 하나 이상 지정할 수 있게 하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- `constraints.accept`는 브라우저의 파일 대화 상자에 표시되는 파일 형식을 지정합니다. 허용되는 값은 [HTML5 사양](https://www.w3.org/TR/html5/forms.html#attr-input-accept)을 참조하세요. 기본값은 **null**입니다.
- `options.multiple`이 **true**로 설정되면 사용자가 브라우저의 파일 대화 상자에서 둘 이상의 파일을 선택할 수 있게 합니다. 기본값은 **false**입니다.
- 이 요소는 `options.uploadMode` 값에 따라 두 가지 모드로 파일 업로드를 지원합니다. **file**을 지정하면 출력에 파일의 내용이 Blob으로 포함됩니다. **url**을 지정하면 파일은 임시 위치에 업로드되고 출력에 Blob의 URL이 포함됩니다. 임시 Blob은 24시간 후에 제거됩니다. 기본값은 **file**입니다.
- 업로드된 파일은 보호됩니다. 출력 URL에는 배포 중에 파일에 액세스하기 위한 [SAS 토큰](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)이 포함됩니다.
- `options.openMode` 값은 파일을 읽는 방법을 결정합니다. 파일이 일반 텍스트여야 하면 **text**를 지정합니다. 그렇지 않으면 **binary**를 지정합니다. 기본값은 **text**입니다.
- `options.uploadMode`를 **file**로 설정하고 `options.openMode`를 **binary**로 설정하면 출력이 base64로 인코딩됩니다.
- `options.encoding`은 파일을 읽을 때 사용할 인코딩을 지정합니다. 기본값은 **UTF-8**이며, `options.openMode`를 **text**로 설정한 경우에만 사용됩니다.

## <a name="sample-output"></a>샘플 출력
options.multiple이 false이고 options.uploadMode가 file이면 출력에 파일의 내용이 JSON 문자열로 포함됩니다.

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

options.multiple이 true이고 options.uploadMode가 file이면 출력에 파일의 내용이 JSON 배열로 포함됩니다.

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

options.multiple이 false이고 options.uploadMode가 url이면 출력에 URL이 JSON 문자열로 포함됩니다.

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

options.multiple이 true이고 options.uploadMode가 url이면 출력에 URL 목록이 JSON 배열로 포함됩니다.
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

CreateUiDefinition을 테스트할 때 일부 브라우저(예: Chrome)에서는 브라우저 콘솔의 Microsoft.Common.FileUpload 요소로 생성된 URL을 자릅니다. 개별 링크를 마우스 오른쪽 단추로 클릭하여 전체 URL을 복사해야 할 수도 있습니다.


## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
