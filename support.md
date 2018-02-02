# Support Requests

- [Introduction](#introduction)
- [Customization](#customization)

<a name="introduction"></a>
## Introduction

> **Note:** This feature is not supported if you do not use Vue as your application's JavaScript framework.

Spark includes a simple way for your customers to contact you with support requests. After installing Spark and registering a new account, the "Email Us" link will be available from the user's account drop-down menu.

To specify where these e-mails should be sent, you should set the `$sendSupportEmailsTo` property on your `SparkServiceProvider` class. Don't forget to also configure your mail settings in Laravel's `config/mail.php` configuration file:

    /**
     * The address where customer support e-mails should be sent.
     *
     * @var string
     */
    protected $sendSupportEmailsTo = 'support@your-application.com';

<a name="customization"></a>
## Customization

If you would like to have more control over how support requests are delivered to your development team, you may swap the `handle` method of the `SendSupportEmail` interaction with your own custom implementation. This will allow you to deliver support requests to the help desk of your choice using a third party API, etc.

To get started, use the `Spark::swap` method within the `booted` method of your `SparkServiceProvider`:

    Spark::swap('SendSupportEmail@handle', function (array $data) {
        // $data['from']
        // $data['subject']
        // $data['message'],
    });
