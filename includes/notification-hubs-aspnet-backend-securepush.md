---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260811"
---
## <a name="webapi-project"></a>WebAPI 프로젝트

1. Visual Studio에서 **사용자에게 알림** 자습서에서 만든 **AppBackend** 프로젝트를 엽니다.
2. Notifications.cs에서 전체 **Notifications** 클래스를 다음 코드로 바꿉니다. 자리 표시자를 알림 허브에 대한 연결 문자열(모든 권한 사용) 및 허브 이름으로 바꿉니다. [Azure Portal](https://portal.azure.com)에서 이러한 값을 가져올 수 있습니다. 이 모듈은 이제 전송할 다른 보안 알림을 나타냅니다. 완전한 구현에서 알림은 데이터베이스에 저장됩니다. 여기서는 단순화를 위해 메모리에 알림을 저장합니다.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. NotificationsController.cs에서, **NotificationsController** 클래스 정의 내의 코드를 다음 코드로 바꿉니다. 이 구성 요소는 디바이스에서 알림을 안전하게 검색할 수 있는 방법을 구현하며, 이 자습서에서는 사용자가 자신의 디바이스로 보안 푸시를 트리거할 수 있는 방법을 제공합니다. 알림 허브로 알림을 보낼 때에는 알림의 ID만 포함된 원시 알림(실제 메시지 없음)을 보냅니다.
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

이제 `Post` 메서드는 알림 메시지를 보내지 않습니다. 중요 콘텐츠가 아닌 알림 ID만 포함하는 원시 알림을 보냅니다. 또한 알림 허브에서 자격 증명을 구성하지 않은 플랫폼에 대한 보내기 작업은 오류를 발생시키므로 이 작업을 주석으로 처리합니다.

1. 이제 모든 디바이스에서 액세스할 수 있도록 이 앱을 Azure 웹 사이트에 다시 배포합니다. **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
2. Azure 웹 사이트를 게시 대상으로 선택합니다. Azure 계정으로 로그인 하 고 기존 또는 새 웹 사이트를 선택 하 고 **연결** 탭에서 **대상 URL** 속성을 기록 합니다. 이 URL은이 자습서의 뒷부분에 있는 *백 엔드 엔드포인트* 으로 참조 합니다. **게시**를 클릭합니다.
