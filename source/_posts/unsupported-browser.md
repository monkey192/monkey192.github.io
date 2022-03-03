---
title: Show msg Unsupported browser
date: 2021-03-03 10:07:38
tags:
    - angular
---

### Issue
[ Internet Explorer 11 support deprecation and removal](https://github.com/angular/angular/issues/41840)

According to announcement from angular, the IE 11 will be removed in the feature. <br />
Is there any way for warning to user who uses IE to open your site?

### Solution
the simple way to do that
- check browser version when your site `onload()`
- if it is unsupported browser version, -> close your site
  

### Sourcecode example
Here is an example source. 
Add it to your index.html file
```javascript
<script type="text/javascript">

      /**
       *  Check Edge is Chromium version.
       */
      function isChromium() {
        let userAgentData = navigator['userAgentData'].brands || [];
        for (let index = 0; index < userAgentData.length; index++) {
          let brand_version_pair = userAgentData[index].brand;
          if (brand_version_pair === 'Chromium') {
            return true;
          }
        }
        return false;
      }

      /**
       * Check support browser
       * Only support: Edge (Chromium) / chrome/ firefox
       */
      function isSupportedBrower() {
        let userAgent = navigator.userAgent;
        if (userAgent.indexOf('Edg') != -1) {
          // MS Edge
          if (isChromium()) {
            return true;
          }
        } else if (userAgent.indexOf('Chrome') != -1) {
          // Google chrome
          return true;
        } else if (userAgent.indexOf('Firefox') != -1) {
          // Firefox
          return true;
        } else {
          return false;
        }
      }

      const confirmBrowserMsg =
        '\n(Website cannot be used with your web browser.' +
        '\nPlease use one of the below latest browsers)' +
        '\n・Firefox' +
        '\n・Google Chrome' +
        '\n・MS Edge (Chromium)';

      window.addEventListener('load', function () {
        if (!isSupportedBrower()) {
          // Check browser
          let r = confirm(confirmBrowserMsg);
          if (r) {
            // Close current tab
            setTimeout(window.close(), 100);
          } else {
            // to continue loading
          }
        }
      });
    </script>
```