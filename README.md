# Dore

> Dore is a WebView container implemented using React Native. Help you migrate Cordova application to React Native WebView.

**Dore 是一个使用 React Native 实现的 WebView 容器，可以让你在 WebView 调用 React Native 组件。**

设计初衷：用于迁移 Cordova 的 WebView 应用到 React Native 的 WebView。

Ionic v1 + Angular Demo: [examples](https://github.com/phodal/dore/tree/master/examples)

Screenshots:

![Screenshots](./doc/demo.jpg)

Workflow:

![Workflow](./doc.workflow.jpeg)

Features
---

### Support:

 - BackHandler (Android)
 - Badge (by [react-native-icon-badge](https://github.com/uuau99999/react-native-icon-badge)
 - Brightness （by [react-native-device-brightness](https://github.com/Calvin-Huang/react-native-device-brightness))
 - Console
 - Clipboard 
 - DatePicker (iOS by [react-native-notag-datepicker](https://github.com/phodal/react-native-notag-datepicker))
 - DeviceInfo (by [react-native-device-info](https://github.com/rebeccahughes/react-native-device-info))
 - Geolocation
 - Keyboard
 - NetInfo
 - Orientation
 - Permissions (by [react-native-permissions](https://github.com/yonahforst/react-native-permissions))
 - State
 - StatusBar
 - Toast (by [dore-toast](https://github.com/GrowthStudio/dore-toast))
 - Vibration
 
### Todo 
 
 - APP Availability (https://github.com/ohh2ahh/AppAvailability)
 - BLE 
 - Calender (https://github.com/wmcmahan/react-native-calendar-events)
 - Camera
 - Flashlight
 - fs
 - Push Notifications (https://github.com/zo0r/react-native-push-notification)
 - QRCode
 - Share
 - SMS
 - Storage
 - more+
 
Usage
---

```bash
yarn add dore
```

Example
---

1.Import to your React-Native WebView

```javascript
...
import Toast from 'dore-toast';
import Orientation from 'react-native-orientation';
import RNIconBadge from 'dore-icon-badge'
import RNDeviceInfo from "react-native-device-info";

import Dore from 'dore';

export default class ExampleWebView extends Component {
  ...
  
  constructor() {
    super()
    ...
    Dore.inject([{
      name: 'Toast',
      class: Toast
    }, {
      name: 'Orientation',
      class: Orientation
    }, {
      name: 'Badge',
      class: RNIconBadge
    }, {
      name: 'DeviceInfo',
      class: RNDeviceInfo
    }])
  }

  onMessage = evt => {
    Dore.handleMessage(evt, this.webView)
  };

  onWebViewLoadStart = () => {
    if (this.state.isLoading) {
      this.webView.injectJavaScript('window.isPhone = true;');
    }
  };

  render() {
    ...

    return (
      <View>
        <WebView
          ...
          onMessage={this.onMessage}
        />
      </View>
    )
  }
}
```

2.import [DoreClient](./client/DoreClient.js), e.x:

```html
<script src="js/promise.js"></script>
<script src="js/DoreClient.js"></script>
```

3.use in JavaScript

```javascript
$scope.showToast = function () {
  DoreClient.showToast('this is a toast')
};
```

APIs
---

### App State

```
DoreClient.addStateListener();
$ionicPlatform.on('STATE', function(event) {
  DoreClient.showToast(event.detail.data);
});
```

### Back（ Android Only)

example:

```
$ionicPlatform.on('ANDROID_BACK', function (event) {
  DoreClient.showToast('ANDROID_BACK');
});
```

### Badge

| function         | return type   |    return             |
-------------------|---------------|-----------------------|
| setBadge         |        -      |  -   |
| getBadge         |  promise      | { badge: 'xx' }     |
| clearBadge       |   -           |     -     |

```
$scope.getBadge = function () {
  DoreClient.getBadge().then(function (data) {
    $scope.badge = data.badge;
    $scope.$apply();
  })
};
$scope.setBadge = function() {
  DoreClient.setBadge(19);
};
$scope.clearBadge = function() {
  DoreClient.clearBadge();
};
```

### Brightness

| function             | return type   |    return             |
-----------------------|---------------|-----------------------|
| getBrightnessLevel   |  promise      | { brightness: 'xx' }  |
| setBrightnessLevel   |       -       |      -                |

```
$scope.getBrightness = function () {
  DoreClient.getBrightnessLevel().then(function(brightness) {
    $scope.brightness = brightness;
    $scope.$apply();
  })
};
$scope.setBrightness = function () {
  DoreClient.setBrightnessLevel(0.2);
};
```

### DeviceInfo

| function         | return type   |    return             |
-------------------|---------------|-----------------------|
| getAppVersion    |  promise      | { version: 'xx' }     |
| getUniqueID      |  promise      | { uniqueID: 'xx' }    |
| getBrand         |  promise      | { brand: 'xx' }       |
| getModel         |  promise      | { model: 'xx' }       |
| getSystemName    |  promise      | { systemName: 'xx' }  |
| isEmulator       |  promise      | boolean               |
| isTablet         |  promise      | boolean               |
 
examples: 
 
```
$scope.getAppVersion = function() {
  DoreClient.getAppVersion().then(function(data) {
    $scope.version = data.version;
    $scope.$apply();
  });
};
$scope.isTablet = function() {
  DoreClient.isTablet().then(function(data) {
    $scope.isTablet = data;
    $scope.$apply();
  });
};
```
All Examples
---

### App State Example

```
DoreClient.addStateListener();
$ionicPlatform.on('STATE', function (event) {
  $scope.state = event.detail.data;
  $scope.$apply();
});
```

```javascript
angular.module('starter.controllers', [])

  .controller('DashCtrl', function ($scope, DoreClient, $ionicPlatform) {
    $scope.version = null;
    $scope.text = 'Text Copy';
    $scope.copyText = '';

    DoreClient.addStateListener();
    $ionicPlatform.on('STATE', function (event) {
      $scope.state = event.detail.data;
      $scope.$apply();
    });

    DoreClient.getAppVersion().then(function (data) {
      $scope.version = data.version;
    });
    $scope.showToast = function () {
      DoreClient.showToast('this is a toast')
    };
    $scope.showToastCenter = function () {
      DoreClient.showToast('this is a toast', 'long', 'center')
    };

    $scope.open = function () {
      DoreClient.open('https://www.phodal.com/')
    };

    $scope.copy = function () {
      DoreClient.copy($scope.text);
    };
    $scope.paste = function () {
      $ionicPlatform.on('PASTE', function (event) {
        $scope.copyText = event.detail.data;
        $scope.$apply();
      });
      DoreClient.paste();
    };

    $scope.getBadge = function () {
      DoreClient.getBadge()
    };
    $scope.setBadge = function () {
      DoreClient.setBadge(19)
    };
    $scope.addBadge = function () {
      DoreClient.addBadge()
    };
    $scope.minusBadge = function () {
      DoreClient.minusBadge()
    };
    $scope.showDatePicker = function () {
      var options = {
        date: '2017-10-22 12:12:12',
        maxDate: '2022-10-22 12:12:12'
      };
      DoreClient.showDatePicker(options).then(function (data) {
        $scope.date = data.date;
      })
    };

    $scope.getCurrentPosition = function () {
      DoreClient.getCurrentPosition().then(function (data) {
        $scope.location = data;
      })
    };
    $scope.watchPosition = function () {
      $ionicPlatform.on('WATCH_POSITION', function (event) {
        $scope.wPosition = event.detail.data;
        $scope.$apply();
      });
      DoreClient.watchPosition()
    };
    $scope.clearWatch = function () {
      DoreClient.clearWatch()
    };
    $scope.stopObserving = function () {
      DoreClient.stopObserving()
    };
    $scope.getOrientation = function () {
      DoreClient.getOrientation().then(function (data) {
        console.log(data);
        $scope.orientation = data;
      })
    };
    $scope.lockToLandscape = function () {
      DoreClient.lockToLandscape();
    };
    $scope.lockToPortrait = function () {
      DoreClient.lockToPortrait();
    };
    $scope.getConnectionInfo = function () {
      DoreClient.getConnectionInfo().then(function (data) {
        $scope.connectionInfo = data;
      })
    };
    $scope.addEventListener = function () {
      $ionicPlatform.on('CONNECTION_CHANGE', function (event) {
        $scope.wConnectionInfo = event.detail.data;
        $scope.$apply();
      });
      DoreClient.addNetInfoEventListener();
    };
    $scope.removeEventListener = function () {
      DoreClient.removeNetInfoEventListener();
    };
    $scope.hideStatusBar = function () {
      DoreClient.hideStatusBar();
    };
    $scope.showStatusBar = function () {
      DoreClient.showStatusBar();
    };
  })
```

Development
---
 
### DoreClient：**Browser -> vibrationVibrate -> DoreClient -> window.postMessage -> RN**

DoreClient, handle message in WebView

Browser

```
$scope.vibrationVibrate = function () {
  DoreClient.vibrationVibrate([1000, 2000, 3000]);
};
```  

DoreClient

```
function invoke(action, payload) {
  function postMessage(action, payload) {
    var message = JSON.stringify({
      action: action,
      payload: payload
    });
    window.postMessage(message, '');
  }
}

DoreClient = {
  vibrationVibrate: function (duration) {
    return invoke('VIBRATION', {type: 'VIBRATE', duration: duration});
  }
}
```

### Dore: WebView -> onMessage -> Dore -> xxxBridge -> Native

Dore, handle message in React Native

WebView

```
  onMessage = evt => {
    Dore.handleMessage(evt, this.webView)
  };
```

Dore

```
Dore.handleMessage = (event, webView) => {
  const action = eventData.action;
  switch (action) {
    case 'VIBRATION': {
      return VibrationBridge(payload)
    }
  }
```

Bridge


```
import { Vibration } from 'react-native';

let VibrationBridge = (payload) => {
  if (payload.type === 'VIBRATE') {
    if (!payload.options) {
      return Vibration.vibrate(500)  // is duration is fixed time (about 500ms)
    }

    if (Number.isInteger(payload.duration) || payload.duration.length > 0) {
      return Vibration.vibrate(payload.duration)
    }
  } else if (payload.type === 'CANCEL') {
    Vibration.cancel();
  }
};
```

License
---

[![Phodal's Idea](http://brand.phodal.com/shields/idea-small.svg)](http://ideas.phodal.com/)

© 2017 A [Phodal Huang](https://www.phodal.com)'s [Idea](http://github.com/phodal/ideas).  This code is distributed under the MIT license. See `LICENSE` in this directory.
