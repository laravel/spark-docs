# Billing

- [Provider Configuration](#provider-configuration)
    - [Webhooks](#webhooks)
- [Configuring Billing Plans](#configuring-billing-plans)
    - [Configuring User Billing Plans](#configuring-billing-plans)
    - [Configuring Team Billing Plans](#configuring-team-billing-plans)
    - [No Credit Card Up Front](#no-credit-card-up-front)
    - [Requiring Credit Cards Up Front](#requiring-credit-cards-up-front)
    - [Collecting Billing Addresses](#collecting-billing-addresses)
    - [Constraining Access To Plans](#constraining-access-to-plans)
    - [Archiving Plans](#archiving-plans)
- [Charging Per Seat](#charging-per-seat)
    - [Charging Users Per Team](#charging-users-per-team)
    - [Charging Teams Per Member](#charging-teams-per-member)
    - [Custom Per Seat Targets](#custom-per-seat-targets)
- [Checking Subscription Status Via Middleware](#checking-subscription-status-via-middleware)
- [Site Wide Promotions](#site-wide-promotions)
- [Currency Customization](#currency-customization)
- [Proration](#proration)
- [Billing Events](#billing-events)

<a name="provider-configuration"></a>
## Provider Configuration

After installing Spark, your application's `.env` file will contain the `STRIPE_KEY` and `STRIPE_SECRET` environment variables that need to be populated. You may retrieve these values from your Stripe control panel.

<a name="webhooks"></a>
### Webhooks

Stripe webhooks should be configured to point to the `/webhook/stripe` URI. Here's the list of event types you need to configure:

- customer.subscription.updated
- customer.subscription.deleted
- customer.updated
- customer.deleted
- invoice.payment_action_required
- invoice.payment_succeeded

<a name="configuring-billing-plans"></a>
## Configuring Billing Plans

### Configuring User Billing Plans

All of your application's billing plans are defined in the `app/Providers/SparkServiceProvider` class. Of course, this service provider is automatically generated when you create a new Spark project. Within the service provider's `boot` method, you will find a sample plan already defined. The default Spark application defines a "no card up front" trial plan, which will allow new users to sign up for your application without providing their billing details during their initial registration.

You may modify this plan and define additional plans as needed. The first argument given to the `plan` method is the displayable name of the plan while the second argument is the plan's ID on Stripe:

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

<a name="configuring-team-billing-plans"></a>
### Configuring Team Billing Plans

Team billing plans allows teams to subscribe to billing plans instead of users. For example, a user may create one team for their business that is subscribed to the "Pro" plan, while having another team for their personal projects that is subscribed to the "Basic" plan.

If your application offers team plans instead of individual user plans, you should generate your application using the `--team-billing` switch. This will generate a `SparkServiceProvider` stub that defines team plans instead of individual plans:

    spark new project-name --team-billing

When your application provides team billing, the `boot` method of your `SparkServiceProvider` should typically contain a configuration like the following:

    Spark::noCardUpFront()->teamTrialDays(10);

    Spark::freeTeamPlan()
        ->features([
            'First', 'Second', 'Third'
        ]);

    Spark::teamPlan('Basic', 'team-basic')
        ->price(10)
        ->features([
            'First', 'Second', 'Third'
        ]);

> **Note:** You may define user and team billing plans within the same application.

#### Limiting Team Members Per Plan

Since it's common to limit the number of team members a given team may have for a given plan, Spark makes configuring these constraints a breeze. You may use the `maxTeamMembers` method when defining your team plan. Once this is configured, Spark will automatically validate that attempted plan changes are valid based on the number of team members allowed for the plan:

    Spark::teamPlan('Basic', 'team-basic')
        ->price(10)
        ->maxTeamMembers(5)
        ->features([
            'Five Team Members',
            'Feature 2',
            'Feature 3',
        ]);

<a name="no-credit-card-up-front"></a>
### No Credit Card Up Front

By default, Spark is configured to allow users to register without providing their credit card. After registering, they may choose to fully subscribe to the application at any time before their trial period ends. The number of trial days granted to a user when they register is defined by the `trialDays` method that is called on the `Spark` instance in the `SparkServiceProvider`. Or, if you are using team billing, you should use the `teamTrialDays` method instead:

    // When using user billing...
    Spark::noCardUpFront()->trialDays(10);

    // When using team billing...
    Spark::noCardUpFront()->teamTrialDays(10);

If a customer registers while Spark is configured in this way, they will be placed on a "generic trial", meaning they will not actually be subscribed to any of your plans. If you would like to know if a customer is on a "no card up front", generic trial, you may use the `onGenericTrial` method on the user (or team if using team billing) instance. This method may be used to limit access to certain features when a user is on a generic trial:

    // Via user...
    $user->onGenericTrial();

    // Via team...
    $team->onGenericTrial();

<a name="requiring-credit-cards-up-front"></a>
### Requiring Credit Cards Up Front

By default, Spark is configured to allow users to register without providing their credit card. After registering, they may choose to fully subscribe to the application at any time before their trial period ends.

However, you may wish to choose to gather credit card details up front. To do so, remove the calls to `noCardUpFront` and `trialDays` / `teamTrialDays` from your `SparkServiceProvider`. This will instruct the registration view to gather the user's credit card details up front.

If you would like to gather credit card details up front but also offer trial periods on your plans, you will need to define the `trialDays` on the plans themselves:

    Spark::plan('Basic', 'team-basic')
        ->price(10)
        ->trialDays(10)
        ->features([
            'Five Team Members',
            'Feature 2',
            'Feature 3',
        ]);

> **Note:** If you define your plans in this manner, users will be **automatically** billed once their trial period expires unless they cancel their subscription.

<a name="collecting-billing-addresses"></a>
### Collecting Billing Addresses

> **Note:** This feature is only supported if your application is using Stripe for subscription billing.

If you would like to collect your customer's billing addresses during registration and during payment method updates, you may call the `collectBillingAddress` method on the Spark instance in the `boot` method of your `SparkServiceProvider`:

    Spark::collectBillingAddress();

<a name="constraining-access-to-plans"></a>
### Constraining Access To Plans

Sometimes you may wish to limit plans by a given resource. For example, perhaps your project management application's "Pro" plan only allows a user to create 20 projects, while your "Enterprise" plan allows users to create up to 50 projects. In this example, a user on the Enterprise plan could not downgrade to the Pro plan if they still have 40 projects attached to their account.

Spark allows you to customize these rules using the `checkPlanEligibilityUsing` method which accepts a Closure as its single argument. This Closure receives the current user and the plan the user is attempting to subscribe to. It is up to you to either return `true` or `false` from this callback based on whether the user should be allowed to change to the given plan. You can call this method from the `boot` method of your application's `SparkServiceProvider`:

    Spark::checkPlanEligibilityUsing(function ($user, $plan) {
        if ($plan->name == 'pro' && count($user->todos) > 20) {
            return false;
        }

        return true;
    });

Alternatively, you may pass a class / method reference:

    Spark::checkPlanEligibilityUsing('EligibilityChecker@handle');

If you would like to provide a specific reason the user is not allowed to switch to a given plan, you may throw a `IneligibleForPlan` exception from the callback. The given reason will be displayed to the user on the subscription update screen:

    use Laravel\Spark\Exceptions\IneligibleForPlan;

    Spark::checkPlanEligibilityUsing(function ($user, $plan) {
        if ($plan->name == 'pro' && $user->todos->count() > 20) {
            throw IneligibleForPlan::because('You have too many to-dos.');
        }

        return true;
    });

<a name="archiving-plans"></a>
### Archiving Plans

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

<a name="charging-per-seat"></a>
## Charging Per Seat

Spark's per-seat pricing makes it possible to charge your customers for each team, for each team member, or by any other custom metric you wish to charge by. For example, if you are building project management software, you may wish to charge your users per project.

To get started, your application should typically have **two** subscription plans: a free plan and a plan that defines the price "per seat".

<a name="charging-users-per-team"></a>
### Charging Users Per Team

If your application uses teams, you may charge your users based on how many teams they manage. When a team is created or removed from a team, Spark will automatically update the subscription's "quantity" on Stripe. To configure this style of billing, the `boot` method of your `SparkServiceProvider` should look like the following:

    Spark::useStripe()->noCardUpFront()->trialDays(10);

    Spark::chargePerTeam();

    Spark::plan('Basic', 'monthly-basic')
        ->price(10)
        ->features([
            'First', 'Second', 'Third'
        ]);

<a name="charging-teams-per-member"></a>
### Charging Teams Per Member

If your application uses teams, you may charge your users based on how many users they add to a team. When a team member is added or removed from a team, Spark will automatically update the subscription's "quantity" on Stripe. To configure this style of billing, the `boot` method of your `SparkServiceProvider` should look like the following:

    Spark::useStripe()->noCardUpFront()->teamTrialDays(10);

    Spark::chargeTeamsPerMember();

    Spark::teamPlan('Basic', 'team-basic')
        ->price(10)
        ->features([
            'First', 'Second', 'Third'
        ]);

<a name="custom-per-seat-targets"></a>
### Custom Per Seat Targets

Next, let's take a look at at charging per a custom target. In this example, let's assume you are building project management software and you would like to charge your customers per project. First, you should call the `chargePerSeat` method within the `boot` method of the `SparkServiceProvider`:

    Spark::chargePerSeat('Projects', function ($user) {
        return $user->projects()->count();
    });

The `chargePerSeat` method accepts two arguments: the name of the item you are charging for and a Closure that calculates the total number of items attached to the user. If you would like to charge teams per seat, you may use the `chargeTeamsPerSeat` method:

    Spark::chargeTeamsPerSeat('Projects', function ($team) {
        return $team->projects()->count();
    });

Next, you will need to call the `addSeat` / `removeSeat` methods from the relevant places in your application. So, for this example, you would call these methods when your users create or delete projects. The `addSeat` method will "increment" your subscription plan's quantity and the `removeSeat` method will "decrement" the subscription plan's quantity:

    $user->addSeat();

    $user->removeSeat();

Alternatively, you may directly provide the number of "seats" the user's subscription currently has:

    $user->updateSeats(5);

Of course, if your application is team based, you should call these methods on a team instance:

    $team->addSeat();

    $team->removeSeat();

    $team->updateSeats(5);

<a name="checking-subscription-status-via-middleware"></a>
## Checking Subscription Status Via Middleware

During installation, Spark automatically registers a `subscribed` middleware in your application's `app\Http\Kernel.php` file. This middleware may be used to restrict access to your application's routes based on the user's subscription status.

To prevent a user from accessing a given route when they are not subscribed to your application, assign the `subscribed` middleware to the route. If the user is not subscribed, they will be redirected to the subscription settings page:

    Route::get('/paid', function () {
        //
    })->middleware('subscribed');

If your application is using team billing, you may check if the current team is subscribed using the `teamSubscribed` middleware:

    Route::get('/paid', function () {
        //
    })->middleware('teamSubscribed');

<a name="site-wide-promotions"></a>
## Site Wide Promotions

If you would like to define a coupon that should be applied to every new registration for your application, you may use the Spark `promotion` method. This allows you to run promotional discounts for holidays or other special occasions. However, the promotional discount may only be applied during new registrations. You may call the `promotion` method from the `boot` method of your `SparkServiceProvider`:

    Spark::promotion('coupon-code');

<a name="currency-customization"></a>
## Currency Customization

Laravel Cashier, which Spark uses to provide subscription billing, supports customizing your application's billing currency. The default Cashier currency is United States Dollars (USD). You can change the default currency by setting the `CASHIER_CURRENCY` environment variable.

> **Note:** The specified currency must be supported by Stripe.

<a name="proration"></a>
## Proration

By default, Spark is configured to charge a prorated amount to the customer if a subscription was changed in the middle of a billing cycle. However, you can disable proration so that no changes are made to the subscription until the beginning of the next billing cycle. To disable proration, call the `noProrate` method in the `boot` method of your `SparkServiceProvider`:

    Spark::noProrate();

<a name="billing-events"></a>
## Billing Events

Spark fires several billing related Laravel events. Several of these events already have listeners assigned to them by default. You can view a list of these listeners in your `App\Providers\EventServiceProvider` class.

**All events listed in the following table are relative to the `Laravel\Spark\Events` namespace:**

Event  | Description
------------- | -------------
`PaymentMethod\BillingAddressUpdated`  |  The customer's billing address has been updated.
`PaymentMethod\VatIdUpdated`  |  The customer's European VAT ID has been updated.
`Subscription\SubscriptionCancelled`  |  A user's subscription was cancelled.
`Subscription\SubscriptionUpdated`  |  A user's subscription was updated.
`Subscription\UserSubscribed`  |  A user started a new subscription to the application.

In addition to these user related subscription events, they are also events for team subscriptions. **All events listed in the following table are relative to the `Laravel\Spark\Events` namespace:**

Event  | Description
------------- | -------------
`Teams\Subscription\SubscriptionCancelled`  |  A team's subscription has been cancelled.
`Teams\Subscription\SubscriptionUpdated`  |  A team's subscription has been updated.
`Teams\Subscription\TeamSubscribed`  |  A team has begun a new subscription.
