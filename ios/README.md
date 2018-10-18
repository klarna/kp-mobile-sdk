# Klarna Payments iOS SDK

<br/>

---
Klarna Payments SDK BETA, API subject to change.

---

<br/>


## Links

<!--To Do
Klarna Payments SDK iOS Reference
https://???
-->
Klarna Payments SDK iOS Example App
https://github.com/klarna/kp-ios-example-app

Klarna Developers
https://developers.klarna.com/en/gb/kco-v3/klarna-payment-methods
(United Kingdom, select your country in the upper right corner)

Klarna API Reference
https://developers.klarna.com/api/#payments-api


## Setup

The Klarna Payments SDK is avaliable through Carthage and Cocoapods.

* Using Carthage, add this to your Cartfile:

    ```swift
    github "klarna/kp-mobile-sdk"
    ```

* Using Cocoapods, add this to your podspec:

    ```swift
    pod "KlarnaPayments"
    ```

* Using the Swift Package Manager, see `Package.swift`:

    ```swift
    name: "KlarnaPayments"
    ```

* Alternatively you can add the framework manually.

<!--To Do
* Remember to link the required frameworks as well.

    ```swift
    SafariServices ???
    WebKit ???
    ```
-->
* Import the framework and get started!

    ```swift
    import KlarnaPayments
    ```


## Integration

The SDK allows developers to integrate a native payments experience into their applications. We offer one way applications may present Klarna Payments where we manage the payment view which you embed into your applications UI layout and handle the initialization, loading, authorization, finalization, sizing and errors.

* To add the payments in your native flow, first create a credit session through Klarna's Payments API, you are then provided with a client token and one or more payment method categories. See https://developers.klarna.com/api/#payments-api-create-a-new-credit-session, the example app shows you how this can be done.

* Then initialize a `KlarnaPaymentView` using the client token and a payment method category identifier.

    ```swift
    paymentView = init(clientToken:, category:, returnUrl:, delegate:)
    ```

* Upon a successful initialization of your payment view, the following `KlarnaPaymentViewDelegate` `klarnaPaymentViewInitialized(paymentView:)` function is called.

* Once the payment view has been successful initialized, you can load it.

    ```swift
        paymentView.load()
    ```

* Once the payment view has been successful loaded, the following `KlarnaPaymentViewDelegate` `klarnaPaymentViewLoaded(paymentView)` function is called, followed by `klarnaPaymentView(paymentView:, resizedToHeight:)`.

* You can now call `paymentView.authorize(autoFinalize:, jsonData:)` to authorize the payment category. If you need to reauthorize call `paymentView.reauthorize(jsonData:)`.

* Once the payment view has been successful authorized or reauthorized, the following `KlarnaPaymentViewDelegate` `klarnaPaymentView(paymentView:, approved:, authorizedWithToken:, finalizeRequired:)` or `klarnaPaymentView(paymentView:, approved:, reauthorizedWithToken:)` function is called.

  * Your payment view is now ready to by finalized if finalization is required, call `paymentView.finalise()`. Please note that this function is called `finalise()` with *s*, and not `finalize()` with *z* which belongs to `NSObject`.

  * Once the payment view has been successful finalized, the following `KlarnaPaymentViewDelegate` `klarnaPaymentView(paymentView:, approved:, finalizedWithToken:)` function is called.

* You can now create a new order with the authorization token through Klarna's Payments API. See https://developers.klarna.com/api/#payments-api-create-a-new-order, the example app shows you how this can be done.

If a Klarna payment error occurred in any step. The `KlarnaPaymentViewDelegate` `klarnaPaymentView(paymentView:, failedWithError:)` function provides an error about what happened and whether it’s recoverable.

You may add the payment view to your applications UI layout once it has been initialized or at a later point. The `KlarnaPaymentViewDelegate` provides several steps where you can find a suitable solution for your applications.

You are responsible for resizing the payment view.


<br/>
