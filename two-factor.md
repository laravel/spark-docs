# Two-Factor Authentication

- [Introduction](#introduction)
- [Usage](#usage)
- [Customization](#customization)

<a name="introduction"></a>
## Introduction

Spark makes it simple to offer two-factor authentication to your users. Two-factor authentication provides your users additional account security by requiring them to provide a token during login in addition to their username and password. This token is generally retrieved from an application or SMS on their cellphone.

By default, Spark uses [Authy](https://authy.com) as the two-factor authentication provider. However, you are free to implement your own two-factor authentication provider if you wish.

Before enabling two-factor authentication, you should populate the `AUTHY_SECRET` environment variable in your application's `.env` file with the value of your Authy API token.

<a name="usage"></a>
## Usage

After configuring your `AUTHY_SECRET` environment variable, you should call the `useTwoFactorAuth` method from the `booted` method of your `SparkServiceProvider`:

    Spark::useTwoFactorAuth();

Calling this method will add the proper two-factor authentication views to the "Security" tab of your user's account profiles. From these views, your users may configure, enable, and disable two-factor authentication for their account.

When two-factor authentication is initially enabled on an account, an emergency login token will be displayed to the user which they should store in a secure password manager. This token may be used to log into the application in the event that the user loses their phone.

<a name="customization"></a>
## Customization

If you would like to implement your own two-factor authentication provider, you may do so by "swapping" the two-factor interactions using the `Spark::swap` method. You will need to swap the following interactions: `EnableTwoFactorAuth`, `DisableTwoFactorAuth`, and `VerifyTwoFactorAuthToken`. You may add these customizations in the `booted` method of your `SparkServiceProvider`:

    Spark::swap('EnableTwoFactorAuth', function ($user, $country, $phone) {
        //
    });

    Spark::swap('DisableTwoFactorAuth', function ($user) {
        //
    });

    Spark::swap('VerifyTwoFactorAuthToken', function ($user, $token) {
        //
    });

You may also swap the interactions with a class and method rather than a Closure:

    Spark::swap('EnableTwoFactorAuth', 'EnableTwoFactorAuthUsingGoogle@handle');

Within these methods, you do not need to worry about persisting the country code and phone number to your database. This will be handled automatically by Spark. Your customized provider methods only need to enable / disable two-factor authentication with the third-party provider of your choice.

For an example of a provider implementation, check out the following classes that are included with Spark:

- `EnableTwoFactorAuthUsingAuthy`
- `DisableTwoFactorAuthUsingAuthy`
- `VerifyTwoFactorAuthTokenUsingAuthy`
