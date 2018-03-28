## Knet Payment Php SDK
[![Packagist License](https://poser.pugx.org/barryvdh/laravel-debugbar/license.png)](http://choosealicense.com/licenses/mit/)
[![Build Status](https://travis-ci.org/iZaL/knet-payment-php.svg?branch=master)](https://travis-ci.org/iZaL/knet-payment-php)

This is a package to integrate KNET Payment Gateway in Php.

## Documentation

## Installation

Require this package with composer:

```shell
composer require izal/knet-payment-php
```

### Usage:

# Initialise the Payment Process
```php
$knetGateway = new KnetBilling([
                'alias'        => 'YOUR_KNET_ALIAS',
                'resourcePath' => '/home/my_web_app_folder/' //Absolute Path to where the resource.cgn file is located
            ]);
```

# configure the URL

```php

$knetGateway->setResponseURL('http://mywebapp.com/payment/response.php');
$knetGateway->setErrorURL('http://mywebapp.com/payment/error.php);
$knetGateway->setAmt(100); 
$knetGateway->setTrackId('123456'); // unique string 

```

Now to accept payments from the customer, first step is to Request the KNET gateway for the payment URL, by calling requestPayment method on the KnetBilling Class,
we have completed our initial step to collect the payment. 


```php
$knetGateway->requestPayment();
```

Second step is to get the payment URL. and to get the payment url just call the method getPaymentURl

```php
/** 
* Get the URL for the Payment and assign it to a variable. this is the URL we will use to redirect the
* customer to payment gateway 
*/

$paymentURL = $knetGateway->getPaymentURL();

```

<pre>Note: If the Payment URL returns null, most probably you messaged up with configuration, or resource path. Check the example in the below section how to ideally request for the payment, which helps to debug for the errors</pre>

Third step is to Redirect the user to the Payment URL
```php
// Redirect the USER to the Payment URL
header('Location: '.$paymentURL);

```

<pre>NOTE: Behind the scenes, Customer will be forwarded to the Knet Payment Page, If the payment process was success, Then he will be redirected to the Response URL we set in setResponseURL method. i.e http://mywebapp.com/payment/response.php</pre>


Fourth step is to get the response returned from the Knet Gateway and redirect the user to success or error page in your application.


```

/**
* to handle the payment response, in http://mywebapp.com/payment/response.php file.
* 
*/

$paymentID = $_POST['paymentid']; 
$result = $_POST['result']; // if the transaction is success, string "CAPTURED" will be return
$tranid = $_POST['tranid']; // transaction id
$trackid = $_POST['trackid'];

// build URL 
$urlParams = "/?paymentID=" . $paymentID . "&transactionID=" . $transactionID . "&trackID=" . $trackID;

if ($result === "CAPTURED") {
    $redirectURL = 'http://mywebapp.com/payment/success.php';
} else {
    $redirectURL = 'http://mywebapp.com/payment/error.php';
}

return "REDIRECT=" . $redirectURL . $urlParams;

/** Above line is very important
 * KNET expects the return value from the page or method to be starting with 
 * REDIRECT=http://mywebapp.com/payment/success/?paymentID.. etc.
 */ 
 

```

This is it. KNET will redirect the USER to the success page in a GET request. i.e http://mywebapp.com/payment/success.php 

