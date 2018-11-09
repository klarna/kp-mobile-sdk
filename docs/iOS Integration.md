# iOS Integration

## Adding the SDK to Your Project

The Klarna Payments SDK is available through Carthage and Cocoapods.

* Using Carthage, add this to your Cartfile:

    ```ruby
    github "klarna/kp-mobile-sdk"
    ```

* Using Cocoapods, add this to your podspec:

    ```ruby
    pod "KlarnaPayments"
    ```

* Alternatively you can add the framework manually.

* Import the framework and get started!

    ```swift
    import KlarnaPayments
    ```

## Integration

1. Create a credit session through Klarna's Payments API, you are then provided with a client token and one or more payment method categories to authorize the session with. See the documentation on [Klarna Developers](https://developers.klarna.com/api/#payments-api-create-a-new-credit-session) for further info on how this can be done.

2. Then create a `KlarnaPaymentView` using the client token and a payment method category identifier. Each payment view corresponds to one of Klarna's payment options a user can choose to pay with.  

    ```swift
    let paymentView = init(clientToken: "my-token", category: "pay-later", returnUrl: "app-schema://schema", delegate: myDelegate)
    ```

    Add this view to your application in whichever way works best with your application struture.

3. If the payment view has been successfuly initialized (you'll be notified through your delegate), you can load the payment method into the view when you're ready.

    ```swift
        // In your delegate

        func klarnaPaymentViewInitialized(_ paymentView: KlarnaPaymentView) {
            paymentView.load()
        }
    ```

4. You'll begin to get calls to your delegate's `resizedToHeight` method as the content in the payment view changes and resizes (e.g. when the user interacts with it). As we don't know how you integrated the view into your application, we delegate resizing the view to you:

    ```swift
    // E.g. if using constraints
    func klarnaPaymentView(_ klarnaPaymentView: KlarnaPaymentView, resizedToHeight height: CGFloat) {
        paymentViewHeightConstraint.constant = height
    }
    ```

4. It's also up to you to track when a user selects a specific payment view. You can, for example, use a Tap Gesture Recognizer. 

5. When a user selects one of the Klarna's payment views and then taps "continue" (or a similar button) in your application, it's time to authorize the session.

    Call `paymentView.authorize(autoFinalize:, jsonData:)` to authorize the session. 

    Your delegate's `authorizedWithToken` method will be called with the results of the approval.

    ```swift
        func klarnaPaymentView(_ paymentView: KlarnaPaymentView, approved: Bool?, authorizedWithToken   authToken: String?, finalizeRequired: Bool?) {

            // If the session is approved, `approved` will be true and you'll get an auth token
            // If the session needs to be finalized, `finalizeRequired` will be true and the token will be nil

        }
    ```


    5.1. If the session details change after the session has been authorized, call `paymentView.reauthorize(jsonData:)`. 
    
    Reauthorization incurs in a network request as Klarna has to process the session's changes, so we recommend you only call this after the user is done performing changes to their session.

    As with authorization, your delegate's `reauthorizedWithToken` method will be called with the results of the reauthorization. 

    5.2. Similarly, if the session needs an additional step to complete the authorization, call `paymentView.finalise()`. Please note that this function is called `finalise()` with *s*, and not `finalize()` with *z* (as in the JS Payments Library) to avoid a conflict with `NSObject` method of the same name. 

    As with authorization, your delegate's `finalizedWithToken` method will be called with the results. 

6. Once you have an authorization token, you can create an order via your backend through Klarna's Payments API. See [Klarna Developers](https://developers.klarna.com/api/#payments-api-create-a-new-order) for more info.

If an error occurred at any step. The `KlarnaPaymentViewDelegate`s `klarnaPaymentView(paymentView:, failedWithError:)` function will provide you with error information describing what happened and whether it’s recoverable (e.g. an e-mail address is invalid).

### App Schema

Some payment methods require authorization through third-party applications. These will return the user to your application upon completion, but to do that you need to supply the app URL that should be used for returning.

You can declare this in the your application's Info.plist.

There do not need to be any special handlers on application load for that url, our only requirement is that the user is returned to your application from the third-party application.

