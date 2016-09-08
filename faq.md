# FAQ

- [Which billing providers does Spark support?](#billing-providers)
- [Which billing intervals does Spark support?](#billing-intervals)
- [Does Spark support one-off charges?](#one-off)
- [Does Spark support user and team subscriptions at the same time?](#user-and-team-billing)
- [Does Spark support limiting the resources for a given plan?](#limiting-resources)
- [Does Spark support collecting European VAT?](#vat)
- [Can Spark be integrated into an existing application?](#existing)

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

Yes, you can limit [team plans](/docs/2.0/billing#configuring-team-billing-plans) by team members. You can also limit plans by [your own arbitrary conditions](/docs/2.0/billing#constraining-access-to-plans).

<a name="vat"></a>
### Does Spark support collecting European VAT?

Yes, Spark [supports collecting European VAT](/docs/2.0/european-vat).

<a name="existing"></a>
### Can Spark be integrated into an existing application?

Spark is not designed to be integrated into existing applications. Spark is designed for new applications.
