# Billing

- [Provider Configuration](#provider-configuration)
- [Configuring Billing Plans](#configuring-billing-plans)
    - [Configuring User Billing Plans](#configuring-billing-plans)
    - [Configuring Team Billing Plans](#configuring-team-billing-plans)
    - [Archiving Plans](#archiving-plans)
- [Constraining Access To Plans](#constraining-access-to-plans)
- [Collecting Billing Addresses](#collecting-billing-addresses)
- [Checking Subscription Status Via Middleware](#checking-subscription-status-via-middleware)
- [No Credit Card Up Front](#no-credit-card-up-front)
- [Requiring Credit Cards Up Front](#requiring-credit-cards-up-front)
- [Site Wide Promotions](#site-wide-promotions)
- [Currency Customization](#currency-customization)
- [Proration](#proration)
- [Billing Events](#billing-events)

<a name="provider-configuration"></a>
## Provider Configuration

After installing Spark, your application's `.env` file will contain environment variables that need to be populated.

If you are using Stripe, you will need to populate the `STRIPE_KEY` and `STRIPE_SECRET` environment variables. You may retrieve these values from your Stripe control panel.

If you are using Braintree, you will need to populate the `BRAINTREE_ENV`, `BRAINTREE_MODEL`, `BRAINTREE_MERCHANT_ID`, `BRAINTREE_PUBLIC_KEY`, and `BRAINTREE_PRIVATE_KEY` variables.

### Webhooks

In order to display a list of your customer's invoices, you must configure the appropriate webhooks on Stripe or Braintree.

Stripe webhooks should be configured to point to the `/webhook/stripe` URI. The Braintree `subscription_canceled`, `subscription_charged_successfully`, and `subscription_expired` webhook events should be configured to point to `/webhook/braintree` URI.

<a name="configuring-billing-plans"></a>
## Configuring User Billing Plans

### The Basics

All of your application's billing plans are defined in the `app/Providers/SparkServiceProvider` class. Of course, this service provider is automatically generated when you create a new Spark project.

Within the service provider's `booted` method, you will find a sample plan already defined. The default Spark application defines a "no card up front" trial plan, which will allow new users to sign up for your application without providing their billing details during their initial registration.

You may modify this plan and define additional plans as needed within this method. The first argument given to the `plan` method is the displayable name of the plan while the second argument is the plan's ID on either Stripe or Braintree:

    Spark::plan('Pro', 'monthly-pro')
            ->price(20)
            ->features([
                'Feature 1',
                'Feature 2',
                'Feature 3',
            ]);

The `features` method accepts an array of features provided by a given plan. These features will be displayed when a user clicks the "Plan Features" modal on the registration or settings views.

If you would like to define a plan that has a yearly billing frequency, you may use the `yearly` method:

    Spark::plan('Pro', 'yearly-pro')
            ->price(100)
            ->yearly()
            ->features([
                'Feature 1',
                'Feature 2',
                'Feature 3',
            ]);

### Limiting Teams Per Plan

If you would like to limit the number of teams a user can create for a given plan, you may use the `maxTeams` method when defining the plan:

    Spark::plan('Pro', 'yearly-pro')
        ->price(100)
        ->yearly()
        ->maxTeams(5);

<a name="configuring-team-billing-plans"></a>
## Configuring Team Billing Plans

Team billing plans allows teams to subscribe to billing plans instead of users. For example, a user may create one team for their business that is subscribed to the "Pro" plan, then create another team for their personal projects that is subscribed to the "Basic" plan.

If your application offers team plans instead of individual user plans, you should generate your application using the `--team-billing` switch:

    spark new project-name --team-billing

This will generate a `SparkServiceProvider` stub that defines team plans instead of individual plans. Of course, team plans may also use methods like `archived`, `features` and `yearly`.

> **Note:** You may even define user and team billing plans in the same application.

### Limiting Team Members Per Plan

Since it's common to limit the number of team members a given team may have for a given plan, Spark makes configuring these constraints a breeze. Simply use the `maxTeamMembers` method when defining your team plan. Once this is configured, Spark will automatically validate that attempted plan changes are valid based on the number of team members allowed for the plan:

    Spark::teamPlan('Basic', 'team-basic')
        ->price(10)
        ->maxTeamMembers(5)
        ->features([
            'Five Team Members',
            'Feature 2',
            'Feature 3',
        ]);

<a name="archiving-plans"></a>
## Archiving Plans

If you no longer wish to offer a given plan to your customers, you should not delete it entirely from your service provider. Instead use the `archived` method when defining the plan. This will prevent new customers from choosing the plan but will allow existing customers to continue using the plan:

    Spark::plan('Pro', 'yearly-pro')
            ->archived()
            ->price(100)
            ->yearly()
            ->features([
                'Feature 1',
                'Feature 2',
                'Feature 3',
            ]);

<a name="constraining-access-to-plans"></a>
## Constraining Access To Plans

Sometimes you may wish to limit plans by a given resource. For example, perhaps your "Pro" plan only allows a user to create 20 to-do lists, while your "Enterprise" plan allows users to create up to 50 to-do lists. In this example, a user on the Enterprise plan could not downgrade to the Pro plan if they still have 40 to-do lists attached to their account.

Spark allows you to customize these rules using the `checkPlanEligibilityUsing` method which accepts a Closure as its single argument. This Closure receives the current user and the plan the user is attempting to subscribe to. It is up to you to either return `true` or `false` from this callback based on whether the user should be allowed to change to the given plan. You can make a call to this method from the `booted` method of your application's `SparkServiceProvider`:

    Spark::checkPlanEligibilityUsing(function ($user, $plan) {
        if ($plan->name == 'pro' && count($user->todos) > 20) {
            return false;
        }
    });

Alternatively, you may pass a class / method reference:

    Spark::checkPlanEligibilityUsing('EligibilityChecker@handle');

If you would like to provide a specific reason the user is not allowed to switch to a given plan, you may throw a `IneligibleForPlan` exception from the callback. The given reason will be displayed to the user on the subscription update screen:

    use Laravel\Spark\Exceptions\IneligibleForPlan;

    Spark::checkPlanEligibilityUsing(function ($user, $plan) {
        if ($plan->name == 'pro' && $user->todos->count() > 20) {
            throw IneligibleForPlan::because('You have too many to-dos.');
        }
    });

<a name="collecting-billing-addresses"></a>
## Collecting Billing Addresses

> **Note:** This feature is only supported if your application is using Stripe for subscription billing.

If you would like to collect your customer's billing addresses during registration and during payment method updates, you may call the `collectBillingAddress` method on the Spark instance in the `booted` method of your `App\Providers\SparkServiceProvider`:

    Spark::collectBillingAddress();

<a name="checking-subscription-status-via-middleware"></a>
## Checking Subscription Status Via Middleware

During installation, Spark automatically registers a `subscribed` middleware in your application's `app\Http\Kernel.php` file. This middleware may be used to restrict access to your application's routes based on the user's subscription status.

To prevent a user from accessing a given route if they are not subscribed to your application, simply assign the `subscribed` middleware to the route. If the user is not subscribed, they will be redirected to the subscriptions settings page:

    Route::get('/paid', ['middleware' => 'subscribed', function () {
        //
    }]);

If you are using team billing, you may check if the current team is subscribed using the `teamSubscribed` middleware:

    Route::get('/paid', ['middleware' => 'teamSubscribed', function () {
        //
    }]);

<a name="no-credit-card-up-front"></a>
## No Credit Card Up Front

By default, Spark is configured to allow users to register without providing their credit card. Then, they may choose to fully subscribe to the application at any time before their trial period ends. The number of trial days granted to a user when they register is defined by the `trialDays` method that is called on the `Spark` instance in the `SparkServiceProvider`. If you are using team billing, you should use the `teamTrialDays` method instead:

    Spark::useStripe()->noCardUpFront()->trialDays(10);

    Spark::useStripe()->noCardUpFront()->teamTrialDays(10);

When a customer registers, they will be placed on a "generic trial", meaning they will not actually be subscribed to one of your plans; however, `$user->subscribed()` and `$user->onTrial()` will both return `true` until the customer's trial expires.

If you would like to know if a customer is on a "no card up front", generic trial, you may use the `onGenericTrial` method on the user (or team if using team billing) instance:

    $user->onGenericTrial();

    $team->onGenericTrial();

<a name="requiring-credit-cards-up-front"></a>
## Requiring Credit Cards Up Front

By default, Spark is configured to allow users to register without providing their credit card. Then, they may choose to fully subscribe to the application at any time before their trial period ends.

However, you may wish to choose to gather credit card details up front. To do so, simply remove the calls to `noCardUpFront` and `trialDays` from your `SparkServiceProvider`. This will instruct the registration view to gather the user's credit card details up front.

If you would like to gather credit card details up front but also offer trial periods on your plans, you will need to define the `trialDays` on the plans themselves:

    Spark::plan('Basic', 'team-basic')
        ->price(10)
        ->trialDays(10)
        ->features([
            'Five Team Members',
            'Feature 2',
            'Feature 3',
        ]);

> **Note:** If you define your plans in this manner, users will be **automatically** billed once their trial period expires unless they cancel their subscription. This method of registration is not recommended because it typically generates many accidental subscriptions when users forget to cancel their trials.

<a name="site-wide-promotions"></a>
## Site Wide Promotions

If you would like to define a coupon to be applied to every new registration for your application, you may use the Spark `promotion` method. This allows you to run promotional discounts for holidays or other special occasions. However, the promotional discount may only be applied during new registrations. You may call the `promotion` method from the `booted` method of your `SparkServiceProvider`:

    Spark::promotion('coupon-code');

<a name="currency-customization"></a>
## Currency Customization

Laravel Cashier, which Spark uses to provide subscription billing, supports customizing your application's billing currency. You may customize the currency by calling the `useCurrency` method on the Cashier instance. The `useCurrency` method accepts the currency abbreviation as its first argument and the currency symbol as its second:

    Laravel\Cashier\Cashier::useCurrency('eur', '€');

You may call this method from the `booted` method of your `App\Providers\SparkServiceProvider` class.

> **Note:** The given currency must be supported by Stripe or Braintree.

<a name="proration"></a>
## Proration

By default Spark is configured to charge a prorated amount to the customer if a subscription was changed in the middle of a billing cycle. However, you can disable proration so that no changes are made to the subscription until the beginning of the next billing cycle, simply call the `noProrate` method in the `booted` method of your `App\Providers\SparkServiceProvider`:

    Spark::noProrate();

<a name="billing-events"></a>
## Billing Events

Spark fires several billing related Laravel events which allow you to perform actions when a given event fires. Several of these events already have listeners assigned to them by default. You can view a list of these listeners in your `App\Providers\EventServiceProvider` class.

**All events listed in the following table are relative to the `Laravel\Spark\Events` namespace:**

Event  | Description
------------- | -------------
`PaymentMethod\BillingAddressUpdated`  |  The customer's billing address has been updated.
`PaymentMethod\VatIdUpdated`  |  The customer's European VAT ID has been updated.
`Subscription\SubscriptionCancelled`  |  A user's subscription was cancelled.
`Subscription\SubscriptionUpdated`  |  A user's subscription was updated.
`Subscription\UserSubscribed`  |  A user started a new subscription to the application.

In addition to these user related subscription events, they are also events for team subscription. **All events listed in the following table are relative to the `Laravel\Spark\Events` namespace:**

Event  | Description
------------- | -------------
`Teams\Subscription\SubscriptionCancelled`  |  A team's subscription has been cancelled.
`Teams\Subscription\SubscriptionUpdated`  |  A team's subscription has been updated.
`Teams\Subscription\TeamSubscribed`  |  A team has begun a new subscription.
