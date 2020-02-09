---
title: Azure Portal를 통해 Azure Data Box Azure Data Box Heavy 관리 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Data Box 및 Azure Data Box Heavy를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/07/2019
ms.author: alkohli
ms.openlocfilehash: 581f95bd813445d2cc9bd83d91917ea83f0bf04f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987481"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Portal를 사용 하 여 Azure Data Box를 관리 하 고 Azure Data Box Heavy

이 문서는 Azure Data Box 및 Azure Data Box Heavy에 모두 적용 됩니다. 이 문서에서는 Azure Data Box 장치에서 수행할 수 있는 몇 가지 복잡 한 워크플로 및 관리 작업에 대해 설명 합니다. Azure Portal 또는 로컬 웹 UI를 통해 Data Box 장치를 관리할 수 있습니다.

이 아티클은 Azure Portal을 사용하여 수행할 수 있는 작업에 중점을 둡니다. Azure Portal를 사용 하 여 주문을 관리 하 고, Data Box 장치를 관리 하 고, 완료 될 때 주문 상태를 추적할 수 있습니다.


## <a name="cancel-an-order"></a>주문 취소

주문을 배치한 후 다양한 이유로 주문을 취소해야 할 수 있습니다. 주문은 처리되기 전까지만 취소할 수 있습니다. 주문이 처리 되 고 Data Box 장치가 준비 되 면 주문을 취소할 수 없습니다.

다음 단계에 따라 주문을 취소합니다.

1.  **개요 > 취소**로 이동합니다.

    ![주문 취소 1](media/data-box-portal-admin/cancel-order1.png)

2.  주문을 취소하는 이유를 채웁니다.  

    ![주문 취소 2](media/data-box-portal-admin/cancel-order2.png)

3.  주문이 취소되면 포털은 주문의 상태를 업데이트하고 **취소됨**으로 표시합니다.

## <a name="clone-an-order"></a>주문 복제

복제는 특정 상황에서 유용합니다. 사용자가 Data Box를 사용하여 데이터를 전송했는데 더 많은 데이터가 생성 되 면 다른 Data Box 장치에서 해당 데이터를 Azure로 전송 해야 합니다. 이 경우 동일한 주문이 복제될 수 있습니다.

다음 단계에 따라 주문을 복제합니다.

1.  **개요 > 복제**로 이동합니다. 

    ![주문 복제 1](media/data-box-portal-admin/clone-order1.png)

2.  주문의 모든 세부 사항을 동일하게 유지합니다. 주문 이름은 *-Clone*으로 추가된 원래 주문 이름입니다. 확인란을 선택하여 개인 정보 취급 방침을 검토한 것을 확인합니다. **만들기**를 클릭합니다.

잠시 후에 복제가 생성되고 포털에서 새 주문을 표시하도록 업데이트합니다.


## <a name="delete-order"></a>주문 삭제

주문이 완료되면 주문을 삭제할 수 있습니다. 주문은 이름, 주소 및 연락처 정보 등의 개인 정보를 포함합니다. 이 개인 정보는 주문이 삭제될 때 삭제됩니다.

완료되거나 취소된 주문을 삭제할 수 있습니다. 주문을 삭제하려면 다음 단계를 수행합니다.

1. **모든 리소스**로 이동합니다. 주문을 검색합니다.

2. 삭제하려는 주문을 클릭하고 **개요**로 이동합니다. 명령 모음에서 **삭제**를 클릭합니다.

    ![Data Box 주문 삭제 1](media/data-box-portal-admin/delete-order1.png)

3. 주문 삭제를 확인하라는 메시지가 나타나면 주문의 이름을 입력합니다. **삭제**를 클릭합니다.

## <a name="download-shipping-label"></a>포장용 레이블 다운로드

Data Box의 E-ink 디스플레이가 작동하지 않아 반송 레이블이 표시되지 않는 경우에는 배송 레이블을 다운로드해야 할 수 있습니다. Data Box Heavy에는 E-잉크가 표시 되지 않으므로이 워크플로는 Data Box Heavy에는 적용 되지 않습니다.

다음 단계에 따라 포장용 레이블을 다운로드합니다.

1.  **개요 > 포장용 레이블 다운로드**로 이동합니다. 이 옵션은 디바이스가 배송된 후에만 사용 가능합니다. 

    ![포장용 레이블 다운로드](media/data-box-portal-admin/download-shipping-label.png)

2.  다음 반송 포장용 레이블을 다운로드합니다. 레이블을 저장 및 인쇄합니다. 레이블을 접어 디바이스의 투명 슬리브 안에 넣습니다. 레이블이 표시되는지 확인합니다. 이전 배송 시 부착된 디바이스의 스티커를 제거합니다.

    ![예제 포장용 레이블](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>배송 주소 편집

주문이 배치되면 배송 주소를 편집해야 할 수 있습니다. 주소는 디바이스가 발송될 때까지만 편집할 수 있습니다. 디바이스가 발송되고 나면 이 옵션은 더 이상 제공되지 않습니다.

주문을 편집하려면 다음 단계를 수행합니다.

1. **주문 세부 정보 > 배송 주소 편집**으로 이동합니다.

    ![배송 주소 편집 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. 배송 주소를 편집하고 유효성을 검사한 다음 변경 내용을 저장합니다.

    ![배송 주소 편집 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>알림 세부 정보 편집

주문 상태 전자 메일을 수신하도록 할 사용자를 변경해야 할 수 있습니다. 예를 들어 디바이스가 배달되거나 수령되면 특정 사용자가 알림을 받아야 하는데, 다른 사용자는 원본에서 삭제하기 전에 데이터가 Azure 스토리지 계정에 있는 것을 확인할 수 있도록 데이터 복사가 완료되면 알림을 받아야 할 수 있습니다. 이러한 경우에 알림 세부 정보를 편집할 수 있습니다.

알림 세부 정보를 편집하려면 다음 단계를 수행합니다.

1. **주문 세부 정보 > 알림 세부 정보 편집**으로 이동합니다.

    ![알림 세부 정보 편집 1](media/data-box-portal-admin/edit-notification-details1.png)

2. 이제 알림 세부 정보를 편집한 다음, 변경 내용을 저장할 수 있습니다.
 
    ![알림 세부 정보 편집 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>주문 기록 다운로드

Data Box 주문이 완료되면 디바이스 디스크의 데이터가 삭제됩니다. 디바이스 정리가 완료되면 Azure Portal에서 주문 내역을 다운로드할 수 있습니다.

주문 내역을 다운로드하려면 다음 단계를 수행합니다.

1. Data Box 주문에서 **개요**로 이동합니다. 주문이 완료되었는지 확인합니다. 주문이 완료되고 디바이스 정리가 완료되면 **주문 세부 정보**로 이동합니다. **주문 기록 다운로드** 옵션을 사용할 수 있습니다.

    ![주문 기록 다운로드](media/data-box-portal-admin/download-order-history-1.png)

2. **주문 기록 다운로드**를 클릭합니다. 다운로드된 기록에는 운송업체 추적 로그 기록이 나와 있습니다. Data Box Heavy의 두 노드에 해당 하는 두 개의 로그 집합이 있습니다. 이 로그의 맨 아래로 스크롤하면 다음 링크를 볼 수 있습니다.
    
   - **로그 복사** - Data Box에서 Azure Storage 계정으로 데이터 복사 중 오류가 발생한 파일 목록이 있습니다.
   - **감사 로그** -Azure 데이터 센터 외부에 있을 때 Data Box에서 공유를 켜고 액세스 하는 방법에 대한 정보를 포함 합니다.
   - **BOM 파일** - **배송 준비 중** 다운로드할 수 있는 파일 목록(또는 파일 매니페스트)과 파일 이름, 파일 크기 및 파일 체크섬이 있습니다.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
       Name                                               : DataBoxTestOrder                              
       StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
       DeviceType                                         : DataBox                                           
       -------------------
       Data Box Activities
       -------------------
       Time(UTC)                 | Activity                       | Status          | Description  
       
       10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
       11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
       11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
       11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
       11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
       11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
       11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
       11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
       11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
       11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
       11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
       11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
       11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
       11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
       11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
       11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
       1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
       1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
       1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
       1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
       1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
       1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
       1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
       1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
       1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
       1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
       1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
       1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
       1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
       1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
       1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
       1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
       1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
       1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
       2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
       2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
       2/4/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

       ------------------
       Data Box Log Links
       ------------------

       Account Name         : Gus                                                       
       Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
       Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
       BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
       ```
     그런 다음, 스토리지 계정으로 이동하여 복사 로그를 볼 수 있습니다.

![스토리지 계정 로그](media/data-box-portal-admin/logs-in-storage-acct-2.png)

또한 감사 로그와 BOM 파일을 포함하는 커스터디 로그 체인도 볼 수 있습니다.

![스토리지 계정 로그](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>주문 상태 보기

포털에서 디바이스 상태가 변경되면 전자 메일을 통해 알림이 전송됩니다.

|주문 상태 |Description |
|---------|---------|
|Ordered     | 주문을 성공적으로 배치했습니다. <br>디바이스 재고가 있으면 Microsoft에서 배송할 디바이스를 확인하여 준비합니다. <br> 디바이스를 즉시 배송할 수 없는 경우에는 디바이스가 배송 가능해지면 주문이 처리됩니다. 주문 처리 과정은 며칠에서 몇 달까지 걸릴 수 있습니다. 90일 이내에 처리할 수 없는 주문은 취소되며 취소 알림이 전송됩니다.         |
|처리됨     | 주문 처리가 완료되었습니다. 주문에 따라 데이터 센터에서 디바이스 배송이 준비됩니다.         |
|발송됨     | 주문이 배송되었습니다. 포털에서 주문에 표시된 추적 ID를 사용하여 배송을 추적합니다.        |
|Delivered     | 배송한 장치가 주문에 지정된 주소로 배달되었습니다.        |
|픽업됨     |반송한 장치가 수령되어 운송업체에서 스캔을 했습니다.         |
|받음     | 디바이스가 Azure 데이터 센터에서 수신되어 스캔되었습니다. <br> 배송된 디바이스 검사가 완료되면 디바이스 업로드가 시작됩니다.      |
|데이터 복사     | 데이터 복사가 진행 중입니다. Azure Portal에서 주문에 대한 복사 진행률을 추적합니다. <br> 데이터 복사가 완료될 때까지 기다립니다. |
|완료       |주문이 성공적으로 완료되었습니다.<br> 서버에서 온-프레미스 데이터를 삭제하기 전에 Azure에 데이터가 있는지 확인합니다.         |
|완료되었지만 오류가 발생했습니다.| 데이터 복사가 완료되었지만 복사 중에 오류가 발생했습니다. <br> Azure Portal에 제공된 경로를 사용하여 복사 로그를 검토합니다. [업로드가 완료 되 고 오류가 발생 한 경우 복사 로그의 예](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-errors)를 참조 하세요.   |
|완료되었으나 경고가 발생함| 데이터 복사가 완료 되었지만 데이터가 수정 되었습니다. 데이터에 파일 또는 blob 이름을 변경 하 여 수정 된 중요 하지 않은 blob 또는 파일 이름 오류가 있습니다. <br> Azure Portal에 제공된 경로를 사용하여 복사 로그를 검토합니다. 데이터의 수정 사항을 기록해 둡니다. [업로드가 완료 되 고 경고가 발생 한 경우 복사 로그의 예](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-warnings)를 참조 하세요.   |
|Canceled            |주문이 취소되었습니다. <br> 주문을 취소했거나 오류가 발생했고 서비스에서 주문을 취소했습니다. 90일 이내에 처리할 수 없는 주문도 취소되며 취소 알림이 전송됩니다.     |
|정리 | 디바이스 디스크의 데이터가 지워집니다. Azure Portal에서 다운로드에 주문 기록을 사용할 수 있게 되면 디바이스 정리가 완료된 것으로 간주됩니다.|



## <a name="next-steps"></a>다음 단계

- [Data Box 및 Data Box Heavy 문제를 해결](data-box-troubleshoot.md)하는 방법을 알아봅니다.
