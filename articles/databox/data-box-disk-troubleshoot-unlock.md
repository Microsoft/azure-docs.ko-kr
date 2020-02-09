---
title: 잠금 해제 문제 디스크 azure Data Box 디스크 문제 해결 | Microsoft Docs
description: Azure Data Box Disk에서 발견된 문제 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148284"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Azure Data Box 디스크에서 디스크 잠금 해제 문제 해결

이 문서에서는 Microsoft Azure Data Box 디스크에 적용 되며 잠금 해제 도구를 사용 하는 경우 문제를 해결 하는 데 사용 하는 워크플로 설명 합니다. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Data Box Disk 잠금 해제 도구 오류


| 오류 메시지/도구 동작      | 권장 사항                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| 현재 .Net Framework는 지원되지 않습니다. 지원되는 버전은 4.5 이상입니다.<br><br>도구는 메시지를 표시하며 종료됩니다.  | .NET 4.5가 설치되어 있지 않습니다. Data Box Disk 잠금 해제 도구를 실행하는 호스트 컴퓨터에서 .NET 4.5 이상을 설치합니다.                                                                            |
| 잠금을 해제하거나 볼륨을 확인할 수 없습니다. Microsoft 지원에 문의하세요.  <br><br>도구가 잠긴 드라이브의 잠금을 해제하거나 확인하지 못했습니다. | 도구는 제공된 암호를 사용하여 잠긴 드라이브의 잠금을 해제할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다.                                                |
| 다음 볼륨의 잠금을 해제하고 확인합니다. <br>볼륨 드라이브 문자: E:<br>werwerqomnf, qwerwerqwdfda 암호를 사용하여 볼륨의 잠금을 해제할 수 없습니다. <br><br>도구는 일부 드라이브의 잠금을 해제하고 성공하거나 실패한 드라이브 문자를 나열합니다.| 부분적으로 성공 제공된 암호를 사용하여 일부 드라이브의 잠금을 해제할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 잠긴 볼륨을 찾을 수 없습니다. Microsoft에서 받은 디스크가 제대로 연결되어 있고 잠긴 상태인지를 확인합니다.          | 도구는 잠긴 드라이브를 찾지 못했습니다. 드라이브는 이미 잠금 해제되거나 검색되지 않습니다. 드라이브가 연결되어 있고 잠겨 있는지 확인합니다.                                                           |
| 심각한 오류: 잘못된 매개 변수<br>매개 변수 이름: invalid_arg<br>사용량:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>예제: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>예제: DataBoxDiskUnlock /SystemCheck<br>예제: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Azure DataBox Disk 순서에서 이 암호를 가져옵니다. 암호는 디스크를 잠금 해제합니다.<br>/Help:           이 옵션은 cmdlet 사용량 및 예제에 대한 도움말을 제공합니다.<br>/SystemCheck:    이 옵션은 시스템이 도구를 실행하기 위한 요구 사항을 충족하는지 확인합니다.<br><br>종료하려면 아무 키나 누르세요. | 잘못된 매개 변수가 입력되었습니다. 허용 된 매개 변수만 /SystemCheck, /PassKey, 및 /Help입니다.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Windows 클라이언트를 사용 하는 경우에 디스크에 대한 문제를 잠금 해제

이 섹션에서는 데이터 복사본에 대한 Windows 클라이언트를 사용 하는 경우 Data Box 디스크의 배포 하는 동안 직면 한 주요 문제 중 일부를 자세히 설명 합니다.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>문제: BitLocker에서 드라이브의 잠금을 해제할 수 없습니다.
 
**원인** 

BitLocker 대화 상자에서 암호를 사용했으며 BitLocker의 드라이브 잠금 해제 대화 상자를 통해 디스크의 잠금을 해제했습니다. 이 작업은 가능하지 않습니다.

**해결 방법**

Data Box Disk의 잠금을 해제하려면 Data Box Disk 잠금 해제 도구를 사용하고 Azure Portal에서 암호를 제공해야 합니다. 자세한 내용은 [자습서: Azure Data Box Disk 압축 풀기, 연결 및 잠금 해제](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)를 참조하세요.
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>문제: 일부 볼륨을 잠금 해제하거나 확인할 수 없습니다. Microsoft 지원에 문의하세요.
 
**원인**

오류 로그에 다음 오류가 표시될 수 있으며, 일부 볼륨을 잠금 해제하거나 확인할 수 없습니다.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
이것은 Windows 클라이언트에서 해당 버전의 Windows PowerShell이 없기 때문일 수 있습니다.

**해결 방법**

[Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616)을 설치하고 작업을 다시 시도할 수 있습니다.
 
그래도 볼륨의 잠금을 해제할 수 없으면 Data Box Disk 잠금 해제 도구가 있는 폴더에서 로그를 복사하고 [Microsoft 지원](data-box-disk-contact-microsoft-support.md)에 문의하세요.

## <a name="next-steps"></a>다음 단계

- 설명 하는 방법 [유효성 검사 문제 해결](data-box-disk-troubleshoot.md)합니다.
