Tradable Embed for iOS - build financial trading capabilities into your apps.
Swift and Objective-C compatible.

Learn more at https://embed.tradable.com.

Read documentation at http://tradable.github.io/ios/docs/.


Steps to use the API:

1.  When your app is ready to use the TradableAPI, call:
  <pre>
  Tradable.authenticateWithAppIdAndUri(appId, uri)
  </pre>
  upon setting the TradableAPIDelegate, where <i>appId</i> is client ID in OAuth flow, and <i>uri</i> is the redirect URI in OAuth flow.
2.  Implement the following UIApplicationDelegate method:
  <pre>
  func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject) -> Bool {
        
    Tradable.sharedInstance.activateAfterLaunchWithURL(url)
        
    return true
  }
  </pre>
3. Listen for <i>tradableReady()</i> delegate method call. The API is ready to be used!
