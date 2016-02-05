Tradable Embed for iOS - build financial trading capabilities into your apps.
Swift and Objective-C compatible.

Learn more at https://tradable.com.

Read documentation at http://tradable.github.io/ios/docs/.


Steps to use the API:

1.  In <i>Info.plist</i>, add a custom URL Scheme - a redirect URI from OAuth flow. When the access token is granted, you will be redirected from Safari to that URI. Make sure it will end up opening your app.

2.  When your app is ready to use the TradableAPI, call:
  <pre>
  Tradable.activateOrAuthenticate(appId, uri, webView)
  </pre>
  upon setting the TradableAPIDelegate, where <i>appId</i> is client ID in OAuth flow, and <i>uri</i> is the redirect URI in OAuth flow; <i>webView</i> is an optional UIWebView component that you might use to start the OAuth flow in - if it’s nil, system browser will be used.
3.  Implement the following UIApplicationDelegate method:
  <pre>
  func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject) -> Bool {
        
    Tradable.sharedInstance.activateAfterLaunchWithURL(url)
        
    return true
  }
  </pre>
4. Listen for <i>tradableReady()</i> auth delegate method call. The API is ready to be used!
