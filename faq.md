# FAQ

- [Which billing providers does Spark support?](#billing-providers)
- [Which billing intervals does Spark support?](#billing-intervals)
- [Does Spark support one-off charges?](#one-off)
- [Does Spark support user and team subscriptions at the same time?](#user-and-team-billing)
- [Does Spark support limiting the resources for a given plan?](#limiting-resources)
- [Does Spark support collecting European VAT?](#vat)
- [Can Spark be integrated into an existing application?](#existing)
- [How do I transition from a Satis-based installation to a local installation?](#move-from-satis-to-path)

<a name="billing-providers"></a>
### Which billing providers does Spark support?

Spark supports Stripe and Braintree.

<a name="billing-intervals"></a>
### Which billing intervals does Spark support?

Spark supports monthly and yearly billing plans, as well as one-off charges.

<a name="one-off"></a>
### Does Spark support one-off charges?

Yes, Spark supports making one-off charges using [Cashier's `invoiceFor` method](https://laravel.com/docs/billing#single-charges).

<a name="user-and-team-billing"></a>
### Does Spark support user and team subscriptions at the same time?

Yes, you can have both user and team plans at the same time.

<a name="limiting-resources"></a>
### Does Spark support limiting the resources for a given plan?

Yes, you can limit [team plans](/docs/7.0/billing#configuring-team-billing-plans) by team members. You can also limit plans by [your own arbitrary conditions](/docs/7.0/billing#constraining-access-to-plans).

<a name="vat"></a>
### Does Spark support collecting European VAT?

Yes, Spark [supports collecting European VAT](/docs/7.0/european-vat).

<a name="existing"></a>
### Can Spark be integrated into an existing application?

Spark is not designed to be integrated into existing applications. Spark is designed for new applications.

<a name="move-from-satis-to-path"></a>
### How do I transition from a Satis-based installation to a local installation?

First, follow the [installation instructions](https://spark.laravel.com/docs/7.0/installation) for the "Spark Installer" package. Once the package has been installed, register your API token using the `register` command. If you do not have an API token, it can be generated from your Spark account dashboard on this website:

    spark register token-value

Next, you should copy the Spark source directory from `vendor/laravel/spark` to `/spark` so that it resides in the root directory of your project. Once you have copied the files, you need to change the Spark dependency version in your `composer.json` file to `@dev`:

    "laravel/spark-aurelius": "@dev",

Finally, update your `composer.json` file to use a `path` repository for Spark. Once this has been completed, run the `composer update` command:

    "repositories": [
        {
            "type": "path",
            "url": "./spark"
        }
    ],
