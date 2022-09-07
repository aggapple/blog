---
title: "［iOS／swift］NotificationServiceExtension에서 다운로드 음원 재생하기"
date: 2022-08-31T12:29:01+09:00
tags: ["iOS","swift","NotificationServiceExtension","sound","Alamofire","AF"]
categories: ["iOS"]
---
####  # Play downloaded sound file in NotificationServiceExtension
***
### 1. 다운로드 저장경로 관련 이슈
- 앱에서 다운로드 파일을 .documentDirectory 경로에 저장 시 NotificationServiceExtension에서 접근되지 않음
> Capabilities의 App Groups를 활용해 파일 접근이 가능하도록 설정

- App Groups를 사용하더라도 Apple에서 지정한 음원 공유용 경로가 아니면 음원을 불러오지 못함
>Answer by KevinE on Jul 30, 2019 5:11 PM
>
>The specifics of how your app should work are going to vary a lot depending on your specific use cases and app design, but our general recommendation for push to talk apps is that you shift to using standard push notifications instead of PushKit for message delivery. More specifically, on the receiver side your Notification Service Extension should download the relevant audio and attach that sound to your particular message.
>
>To support that approach, starting in iOS 13, the system looks for possible sound files in the apps group container(s) as well as the preexisting search locations. The documentation hasn't been updated for this yet (), but there is header file documentation describing the details in the comments for "soundNamed:" in "UNNotificationSound.h":
>
>// The sound file to be played for the notification. <u><b>The sound must be in the Library/Sounds folder of the app's data container or the Library/Sounds folder of an app group data container.</b></u> If the file is not found in a container, the system will look in the app's bundle.
>
>Breaking that down in detail, we look for sound files in the following order and locations:
>
>Your apps direct container in "Library/Sounds". Your apps group(s) directory in a directory named "Library/Sounds" Your apps bundle The main things to keep in mind here:
>
>Directory #2 is "Library/Sounds", not just "Sounds". You'll need to create a "Library" directory with a "Sounds" directory inside it, not just a "Sounds" directory. The apps direct container is still the first place we'll look, so you'll need to be careful about how you name your files. I would recommend either using the group directory for all sounds or following a naming convention for the two different locations so that they never collide. Your Network Service Extension can't see the contents of directory #1, so it can't know whether or not a particular name will collide.

 출처 : https://stackoverflow.com/questions/48379757/ios-play-remote-audio-file-from-notification-extension


 ***
 ### 2. Alamofire(AF)를 이용한 음원(wav) 다운로드
- Alamofire(AF) v5.6.1

```
func soundFileDownloadRequest() {
    let url = "https://domain.com/path/sound.wav"

    let appGroupId = "group.app.id" // AppGroupId는 group. 으로 설정
    let soundFolderPath = "/Library/Sounds/" // 폴더 경로 설정 필수
    let fileName = URL(string: url)!.lastPathComponent
    let fileManager = FileManager.default
    if let filePathUrl = fileManager.containerURL(forSecurityApplicationGroupIdentifier: appGroupId)?.path
      .appending(soundFolderPath).appending(fileName) {
        var isDirectory: ObjCBool = false
        let isFileExist = fileManager.fileExists(atPath: filePathUrl, isDirectory: &isDirectory)
        if isFileExist {
            // 다운로드받은 파일이 존재합니다.
        } else {
            // 파일 다운로드
            let destination: DownloadRequest.Destination = { _, _ in
                return (URL.init(fileURLWithPath: filePathUrl), [.removePreviousFile, .createIntermediateDirectories])
            }
            AF.download(url, method: .get, to: destination).response { (response) in
                if (response.error == nil) {
                    // 다운로드 성공
                } else {
                    // 다운로드 실패
                }
            }
        }
    }
}
```

***
### 3. NotificationServiceExtension에서 다운로드 받은 음원(wav) 재생

```
override func didReceive(_ request: UNNotificationRequest, withContentHandler contentHandler: @escaping (UNNotificationContent) -> Void) {
    bestAttemptContent = (request.content.mutableCopy() as? UNMutableNotificationContent)
    // 이하 생략..

    let appGroupId = "group.app.id"
    let soundFolderPath = "/Library/Sounds/"
    let fileName = "sound.wav"
    let fileManager = FileManager.default
    if let filePathUrl = fileManager.containerURL(forSecurityApplicationGroupIdentifier: appGroupId)?.path
      .appending(soundFolderPath).appending(fileName) {
        var isDirectory: ObjCBool = false
        let isFileExist = fileManager.fileExists(atPath: filePathUrl, isDirectory: &isDirectory)
        if isFileExist {
            // 다운로드 음원 재생
            bestAttemptContent.sound = UNNotificationSound(named: UNNotificationSoundName(rawValue: fileName))
        } else {
            // default 음원 재생 (default.wav가 있는 경우)
            bestAttemptContent.sound = UNNotificationSound(named: UNNotificationSoundName(rawValue: "default.wav"))
        }
    }
}
```

---
