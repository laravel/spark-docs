# European VAT

- [Introduction](#introduction)
- [Enabling European VAT Collection](#enabling-european-vat-collection)
- [Customizing Tax Rate Calculation](#customizing-tax-rate-calculation)

<a name="introduction"></a>
## Introduction

Businesses based in the European Union are required to collect tax based on the location of the customer. In addition, you are required to gather two proofs of the customer's location and use sequential invoice numbers. Spark handles all of these things for you, truly making it a breeze to get started building your next big idea even if you have to comply with obtuse European VAT rules.

> **Note:** Laravel Spark is not a replacement for a good accountant and is not liable for any accounting mistakes or oversights. Laravel Spark does not guarantee 100% compliance with EU VAT regulations. We gave it our best shot.

<a name="enabling-european-vat-collection"></a>
## Enabling European VAT Collection

> **Note:** European VAT collection is only supported when using the Stripe billing provider.

Before enabling European VAT collection, you will need to install the `mpociot/vat-calculator` package via Composer:

    composer require mpociot/vat-calculator

You can enable collection of European VAT by calling the `collectEuropeanVat` method from the `booted` method of your application's `SparkServiceProvider`. The `collectEuropeanVat` method accepts one argument, which is the home country of your business:

    Spark::collectEuropeanVat('GB');

Calling this method is all you need to do to enable European VAT collection. Your customers will automatically be prompted for their billing address on registration, and their billing address will be compared to their credit card's bank country of origin to gather two form of location proofs. The customer will not be able to register or subscribe if these two pieces of information do not match. Spark will also allow customers to supply a VAT ID if they are registering on behalf of a VAT registered company.

When a customer updates their billing information or VAT ID in your application, Spark will automatically call Stripe to update the subscription with the new correct tax rate percentage, so you do not have to do this manually.

For more information on customizing the currency used by your application, check out the [billing documentation](/docs/6.0/billing#currency-customization).

> **Note:** You should be sure to configure a webhook in your Stripe control panel to point to the `webhook/stripe` URI of your application. Otherwise, invoice information will not be gathered for customer payments.

<a name="customizing-tax-rate-calculation"></a>
## Customizing Tax Rate Calculation

By default, Spark uses the [mpociot/vat-calculator](https://github.com/mpociot/vat-calculator) package to calculate VAT rates, but you may use any service you wish by overriding the `taxPercentage` method of your `User` and `Team` models:

    public function taxPercentage()
    {
        return 20; // Customers should be charged 20% tax
    }

Note that within the `taxPercentage` method you may access the customer's billing address, VAT ID, and billing card country of origin:

    public function taxPercentage()
    {
        // $this->billing_country;
        // $this->card_country;
        // $this->vat_id;
    }
