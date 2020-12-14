---
title: AppEase
summary: A watchOS application that predicts and helps alleviate anxiety attacks.
tags:
- iOS
- Swift
- WatchConnectivity
- Kafka
- Django
date: "2020-10-29T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption:
  focal_point: Smart

links:
- icon: github
  icon_pack: fab
  name: Code Repository
  url: https://github.com/vidit23/AppEase
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---

Inspired by Netflix's [Atypical](https://www.netflix.com/title/80117540), my team and I built this application, in conjunction with NYU Meyers, to help kids with ADHD/ASD manage their anxiety. Initially, we planned on using real-time heart rate, activity monitoring, and location to predict potential anxiety attacks and then suggest activities to appease them.

My first observation diving into this project was the lack of resources available online to guide us in using the latest versions of XCode, Swift, and SwiftUI. To dip my toes in the MVVM model, I tried coding a simple game that requires users to match similar cards. I gained the ability to read and understand Apple's documentation as well as manipulate basic UI elements.

To start, we built a minimal web-platform with an authentication API that allowed sign-up and login. With this, each user had a unique token so we could use it to differentiate future streaming data. On the iOS side, we authenticated using an HTTP request to get the corresponding user token [[code]](https://github.com/vidit23/AppEase/blob/main/AppEase/AppEase/LoginManager.swift#L31). The user token is then stored using [UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults) which is a permanent key-value store provided to each application till it is deleted, allowing us to maintain sessions. A message is sent, subsequently, to the Apple Watch by updating the [application context](https://developer.apple.com/documentation/watchconnectivity/wcsession/1615643-applicationcontext) to transfer the login state to the watch. 

Secondly, we built the function to fetch real-time biometric readings. We had two options. First, start a [workout session](https://developer.apple.com/documentation/healthkit/hkworkoutsession) which would allow us to get constant heart rate updates but is battery draining. Second, to construct an AnchorQuery which monitors the HealthKit and notifies our application of any changes. The downside of this approach is that the query works only if our application is in the foreground. A workaround is to use an ObserverQuery that monitors changes in the background too however it is not available for WatchOS due to power limitations. A quick fix was to use a sort of checkpoint stored in UserDefaults (a permanent store for the application till it is deleted) to store the last time the data was read. Once the AnchoredQuery is called, we also call the CoreMotion Manager to get the activity states, steps, and distance in the past thirty seconds of the heart rate update. [[code]](https://github.com/vidit23/AppEase/blob/main/AppEase/AppEase%20WatchKit%20Extension/SensorData/Health/HealthStore.swift#L84).

Limited by the capabilities of the Apple Watch, we could not stream data directly so we write each reading onto a temporary file [[code]](https://github.com/vidit23/AppEase/blob/main/AppEase/AppEase%20WatchKit%20Extension/SensorData/Health/HealthStore.swift#L165) and periodically send it to the paired iPhone if it is reachable. Once the file is transferred, we move it to a folder in the documents directory as it gets deleted after it exits the receiver function [[code]](https://github.com/vidit23/AppEase/blob/main/AppEase/Shared/WatchConnectivityManager.swift#L149). At a significantly greater interval, we read all the files in the documents directory and stream it line by line using a Kafka producer client. [[code]](https://github.com/vidit23/AppEase/blob/main/AppEase/AppEase/KafkaManager.swift#L45).

 We made a Kafka consumer in Python which runs on the server and constantly reads messages from the queue and writes it to the SQLite DB. [[code]](https://github.com/vidit23/AppEase/blob/main/kafkaConsumer.py). We then extended the platform to help show users a graph of their activity and heart-rate which can help doctors monitor their patients.

 Finally, we built in a notification system that would detect anomalies in user behavior using the acquired user information, current heart-rate, and activity data which urged users to play our designed games or listen to calming music within the app to distract them. It also urges them to call help if needed.

 This was an amazing learning experience and my first project building a Mobile Application. 


