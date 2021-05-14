---
title: Azure Data Box Disk 데이터 복사 문제 해결 | Microsoft Docs
description: 로그를 사용하여 Azure Data Box Disk에서 데이터를 복사하는 동안 표시되는 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 5d977fe0b7459af35f678e77681d3b27c31431cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85849177"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Azure Data Box Disk의 데이터 복사 문제 해결

이 문서는 Microsoft Azure Data Box Disk에 적용되며 데이터를 디스크에 복사할 때 발생하는 문제를 해결하는 방법에 대해 설명합니다. 또한 이 문서에서는 분할 복사 도구를 사용할 때의 문제에 대해서도 설명합니다.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Linux 시스템을 사용할 때의 데이터 복사 문제

이 섹션에서는 Linux 클라이언트를 사용하여 데이터를 디스크에 복사할 때 마주하는 몇 가지 주요 문제에 대해 자세히 설명합니다.

### <a name="issue-drive-getting-mounted-as-read-only"></a>문제: 드라이브가 읽기 전용으로 탑재됨
 
**원인** 

정리되지 않은 파일 시스템 때문일 수 있습니다.

Data Box Disk에서는 드라이브를 읽기-쓰기로 다시 탑재할 수 없습니다. 이 시나리오는 dislocker으로 암호 해독한 드라이브에서 지원되지 않습니다. 다음 명령을 사용하여 디바이스를 다시 탑재했을 수 있습니다.

```
# mount -o remount, rw /mnt/DataBoxDisk/mountVol1
```

다시 탑재에 성공했더라도 데이터가 유지되지 않습니다.

**해결 방법**

Linux 시스템에서 다음 단계를 수행합니다:

1. ntfsfix 유틸리티용 `ntfsprogs` 패키지를 설치합니다.
2. 잠금 해제 도구로 드라이브에서 제공된 탑재 지점을 분리합니다. 탑재 지점의 수는 드라이브마다 다를 수 있습니다.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. 해당 경로에서 `ntfsfix`을 실행합니다. 강조 표시된 숫자는 2 단계와 동일해야 합니다.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. 다음 명령을 실행하여 탑재 문제를 일으킬 수 있는 최대 절전 모드 메타 데이터를 제거합니다.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. 완전히 분리합니다.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. 완전히 잠금 해제하고 탑재합니다.
7. 파일을 작성하여 탑재 지점을 테스트합니다.
8. 파일 지속성의 유효성 검사를 위해 분리 및 다시 탑재합니다.
9. 데이터 복사를 계속합니다.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>문제: 복사 후 데이터가 지속되지 않는 문제
 
**원인** 

드라이브를 분리한 후 드라이브에 데이터가 없으면(데이터를 복사한 경우에도) 드라이브를 읽기 전용으로 탑재한 후에 읽기/쓰기로 다시 탑재한 것일 수 있습니다.

**해결 방법**
 
이 경우 [드라이브를 읽기 전용으로 탑재](#issue-drive-getting-mounted-as-read-only)에 대한 해결 방법을 참조하세요.

그 외의 경우에는 Data Box Disk 잠금 해제 도구가 있는 폴더에서 로그를 복사하고 [Microsoft 지원](data-box-disk-contact-microsoft-support.md)에 문의하세요.


## <a name="data-box-disk-split-copy-tool-errors"></a>Data Box Disk 분할 복사 도구 오류

분할 복사 도구를 사용하여 데이터를 여러 디스크로 분할할 때 나타나는 문제는 다음 표에 요약되어 있습니다.

|오류 메시지/경고 |권장 사항 |
|---------|---------|
|[정보] 볼륨: m에 대한 BitLocker 암호 검색 중 <br>[오류] 볼륨: m에 대한 BitLocker 키를 검색하는 동안 예외가 발생함:<br> 시퀀스에 요소가 없습니다.|이 오류는 대상 Data Box Disk가 오프라인인 경우 발생합니다. <br> 온라인 디스크에 `diskmgmt.msc` 도구를 사용합니다.|
|[오류] 예외가 발생함: WMI 작업 실패:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=제공된 복구 암호의 형식이 잘못되었습니다. <br>BitLocker 복구 암호는 48자릿수입니다. <br>복구 암호 형식이 올바른지 확인하고 다시 시도하십시오.|Data Box Disk 잠금 해제 도구를 사용하여 먼저 디스크 잠금을 해제하고 명령을 다시 시도합니다. 자세한 내용은 다음을 참조하세요. <li> [Windows 클라이언트에 대한 Data Box Disk 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) </li><li> [Linux 클라이언트에 대한 Data Box Disk 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) </li>|
|[오류] 예외가 발생함: 대상 드라이이브에 DriveManifest.xml 파일이 존재합니다. <br> 이는 다른 저널 파일을 사용하여 대상 드라이브가 준비되었을 수 있음을 나타냅니다. <br>동일한 드라이브에 데이터를 더 추가하려면 이전 저널 파일을 사용합니다. 기존 데이터를 삭제하고 새 가져오기 작업의 대상 드라이브를 다시 사용하려면 드라이브에서 *DriveManifest.xml* 을 삭제합니다. 이 명령을 새 저널 파일을 사용하여 다시 실행합니다.| 이 오류는 다수의 가져오기 세션에 대해 동일한 드라이브 집합을 사용하려고 할 때 수신됩니다. <br> 하나의 드라이브 집합은 하나의 분할 및 복사 세션에만 사용하십시오.|
|[오류] 예외가 발생함: CopySessionId importdata-sept-test-1은 이전 복사 세션을 참조하며 새 복사 세션에 재사용 할 수 없습니다.|이 오류는 새 작업에 대해 이전에 성공적으로 완료된 직업과 동일한 작업 이름을 사용하려고 시도하면 보고됩니다.<br> 새 작업에 대해 고유한 이름을 제공하십시오.|
|[정보] 대상 파일 또는 디렉터리 이름이 NTFS 길이 한도를 초과합니다. |이 메시지는 파일 경로가 길어서 대상 파일의 이름이 바뀐 경우 보고됩니다.<br> 이 동작을 제어하려면 `config.json` 파일에서 disposition 옵션을 수정하십시오.|
|[오류] 예외가 발생함: JSON 이스케이프 시퀀스가 잘못되었습니다. |이 메시지는 config.json에 유효하지 않은 형식이 있을 때 보고됩니다. <br> 파일을 저장하기 전에 [JSONlint](https://jsonlint.com/)를 사용하여 `config.json`의 유효성을 검사하십시오.|


## <a name="next-steps"></a>다음 단계

- [유효성 검사 도구 문제 해결](data-box-disk-troubleshoot.md) 방법을 알아봅니다.
