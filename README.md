Tradable Embed for iOS - build financial trading capabilities into your apps.
Swift and Objective-C compatible.

Learn more at https://tradable.com.

Read documentation at http://tradable.github.io/ios/docs/.


Steps to use the API:

1.  In <i>Info.plist</i>, add a custom URL Scheme - a redirect URI from OAuth flow. When the access token is granted, you will be redirected from Safari to that URI. Make sure it will end up opening your app.

2.  When your app is ready to use the TradableAPI, upon setting the TradableAuthDelegate, call:
  - for OAuth flow (Live accounts):
    <pre>
    Tradable.activateOrAuthenticate(appId, uri, webView)
    </pre>
    where <i>appId</i> is client ID in OAuth flow, and <i>uri</i> is the redirect URI in OAuth flow; <i>webView</i> is an optional UIWebView component that you might use to start the OAuth flow in - if it’s nil, system browser will be used.
  - for direct API flow (Demo accounts):
    <pre>
    Tradable.activateOrCreate(appId, accountType)
    </pre>
    where <i>appId</i> is your client ID, and <i>accountType</i> is the type of demo account (FOREX or STOCKS).
3.  Implement the following UIApplicationDelegate method:
  <pre>
  func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject) -> Bool {
        
    Tradable.sharedInstance.activateAfterLaunchWithURL(url)
        
    return true
  }
  </pre>
4. Listen for <i>tradableReady(forAccount: TradableAccount)</i> auth delegate method call. The API is ready to be used!


<br />
Handling multiple updates:

Should you be in need to show data across user's accounts, you can invoke 'startUpdates(...)' method for as many accounts as you wish. The TradableEventDelegate's callbacks come back with objects such as e.g. TradableAccountSnapshot which contain 'forAccount' field of type TradableAccount. This way you know what account you have received an update for.
<br />
Consider the following example:

<pre>
import UIKit

import TradableAPI

class ViewController: UIViewController, TradableAuthDelegate, TradableEventsDelegate {

    var accounts:[TradableAccount] = []

    override func viewDidLoad() {
        super.viewDidLoad()
        
        Tradable.sharedInstance.authDelegate = self
        Tradable.sharedInstance.delegate = self

        for _ in 0..<3 {
            Tradable.sharedInstance.createDemoAccount(TradableDemoAPIAuthenticationRequest(appId: 100007, type: TradableDemoAccountType.STOCKS), completion: { (accessToken, error) in
                if let accessToken = accessToken {
                    Tradable.sharedInstance.activateAfterLaunchWithAccessToken(accessToken)
                }
            })
        }
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
    
    func tradableReady(forAccount: TradableAccount) {
        print("Tradable ready for account id = \(forAccount.uniqueId)")
        
        var i = 0
        for account in accounts {
            if account.uniqueId == forAccount.uniqueId {
                break
            }
            i += 1
        }
        if i == accounts.count {
            print("Account with id = \(forAccount.uniqueId) has been added.")
            accounts.append(forAccount)
            
            Tradable.sharedInstance.startUpdates(forAccount, updateType: TradableUpdateType.Full, frequency: TradableUpdateFrequency.ThreeSeconds, symbols: TradableSymbols(symbols: [TradableSymbols.ALL_OPEN_POSITIONS]))
            print("Started updates for account id = \(forAccount.uniqueId).")
        }
    }
    
    func tradableAuthenticationError(error: TradableError) {
        print(error)
    }

    func tradableAccountMetricsUpdated(metrics: TradableAccountMetrics) {
        print("Received metrics for account id = \(metrics.forAccount.uniqueId).")
        
        for account in accounts {
            if account.uniqueId == metrics.forAccount.uniqueId {
                print("Equity = \(metrics.equity)")
            }
        }
    }
    
    func tradableUpdateError(error: TradableError) {
        print(error)
    }
}
</pre>
