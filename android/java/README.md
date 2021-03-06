# Payment Mobile SDK integration example

## Summary

The aim of this repository is to explain how to integrate the Payment Mobile SDK into an Android application using Java.


## Table of contents

- [Payment Mobile SDK integration example](#payment-mobile-sdk-integration-example)
  - [Summary](#summary)
  - [Table of contents](#table-of-contents)
  - [Prerequisites](#prerequisites)
  - [Getting started](#getting-started)
    - [Execute this sample](#execute-this-sample)
    - [How does it work](#how-does-it-work)
  - [Technology](#technology)
  - [Troubleshooting](#troubleshooting)
  - [Copyright and license](#copyright-and-license)

## Prerequisites

In order to be able to perform a successful payment with our Mobile SDK you must have: 
* A contract with your Payment service provider
* A deployed server capable to communicate with the payment platform, in order to verify data and create the payment session  (please check out java server sample or the integration documentation for more information).
* Your public key to initialize the SDK. This key can be found in the merchant back-office in Settings -> Shop -> API -> REST API Keys
* Your REST API Server Name to initialize the SDK. This key can be found in the merchant back-office in Settings -> Shop -> API -> REST API Keys

## Getting started

### Execute this sample

1. Clone the repo, `git clone REPO_URL`. 

2. Open the project under Android Studio

3. Edit the following fields in `MainActivity.java`
    - SERVER_URL: replace by your merchant server Url.
    - PUBLIC_KEY: replace with your public key that you can find in your back-office.
    - REST_API_SERVER_NAME: replace with your REST API server name that you can find in your back-office.
    - SERVER_AUTH_USER: replace with your user value for basic authentication in merchant server.
    - SERVER_AUTH_TOKEN: replace with your password value for basic authentication in merchant server.

4. Run it and that's all! :)

### How does it work

#### Initialize the SDK

It is necessary and important to call the `initialize` method of the SDK on the start of your application. 

```java
//Configure SDK options
HashMap options = new HashMap();
options.put(Lyra.OPTION_API_SERVER_NAME, "MY_API_SERVER_NAME");

//Initialize Payment SDK
Lyra.INSTANCE.initialize(getApplicationContext(), PUBLIC_KEY, options);
```

The "options" parameter corresponds to a Map that allows you to configure the behavior of the SDK. The expected keys in this dictionary are:

| Key                   | Value format | Description                                                        | Required   |
| :-------------------- | :----------- | :----------------------------------------------------------------- | :--------|
| apiServerName         | String       | Your REST API server name that you can find in your back-office.   | Required |
| cardScanningEnabled   | Bool         | Enable/Disable the scan card functionality. If not set, the functionality will be disable. | Optional |
| nfcEnabled            | Bool         | Enable/Disable the NFC card functionality. If not set, the functionality will be disable.  | Optional |

#### Make a payment

Before calling the `process` method of the SDK to process the payment,  it is necessary to, first, create a session using your server.
In this sample, this is done by the `getPaymentContext` method:

```java
requestQueue.add(new JsonObjectRequest(Request.Method.POST, SERVER_URL + "/createPayment", getPaymentParams(), new Response.Listener<JSONObject>() {
    //Process merchant server response
    @Override
    public void onResponse(JSONObject response) {
        //In this sample, the processPayment checks the response and will call the process method of the SDK if the response is good.
        processPayment(response);
    }
}, new Response.ErrorListener() {
    //Error when calling merchant server
    @Override
    public void onErrorResponse(VolleyError error) {
       //Please manage your application error behavior here
       Toast.makeText(getApplicationContext(), "Error Creating Payment", Toast.LENGTH_LONG).show();
    }
}));
```

In this sample, in case of error calling the server, a toast will be displayed with the error text.
  
Otherwise, the `processPayment` method is called with the server response. The server response is checked and the `process` SDK method is called.

```java
Lyra.INSTANCE.process(getSupportFragmentManager(), createResponse.toString(), new LyraHandler() {
    @Override
    public void onSuccess(LyraResponse lyraResponse) {
        //Check the response integrity by verifying the hash on your server
        verifyPayment(lyraResponse);
    }

    @Override
    public void onError(LyraException e, LyraResponse lyraResponse) {
        //Manage error here, please refer to the documentation for more information
        Toast.makeText(getApplicationContext(), e.getErrorMessage(), Toast.LENGTH_LONG).show();
    }
});
```

The SDK will guide the user through the payment process. When the payment succeed, you will have to check the response integrity on your server. 


*Please check official integration documentation for further information and to check other SDK modes and functionality.* 


## Technology

Developed in Android Studio 3.5 and written in Java 7 compatible code, this sample app requires Android API 19 or superior.
For simplicity reasons, in order to make HTTP request, this example uses [Volley library](https://github.com/google/volley).

## Troubleshooting

Check official integration documentation in order to check all possible error codes.

## Copyright and license
	The MIT License

	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:

	The above copyright notice and this permission notice shall be included in
	all copies or substantial portions of the Software.

	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
	THE SOFTWARE.
