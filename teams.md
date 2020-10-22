# Teams

- [Introduction](#introduction)
    - [Changing How Spark Refers To Teams](#changing-how-spark-refers-to-teams)
- [Accessing The User's Teams](#accessing-the-users-teams)
- [Accessing Teams By Path](#accessing-teams-by-path)
- [Team Roles](#team-roles)
- [Team Billing](#team-billing)
- [Using Teams Without Team Billing](#using-teams-without-team-billing)
- [Team Events](#team-events)

<a name="introduction"></a>
## Introduction

Spark includes support for allowing your users to create and join teams. Teams provide a great way for users to share access to resources with other users of your application. Once a team has been created, users may invite other users to their team as well assign them roles.

To use teams, initialize your Spark project using the `--team-billing` flag:

    spark new project-name --team-billing

If you installed your application without the `--team-billing` flag but still want to enable teams later, you may simply add the `Laravel\Spark\CanJoinTeams` trait to your `User` model. In addition, you may wish to review the documentation on [team billing](/docs/11.0/billing#configuring-team-billing-plans):

    <?php

    namespace App;

    use Laravel\Spark\CanJoinTeams;
    use Laravel\Spark\User as SparkUser;

    class User extends SparkUser
    {
        use CanJoinTeams;
    }

After registration, your users will be able to create additional teams from their account dashboard; however, you can disable the creation of additional teams by adding the following to the `boot` method of your `SparkServiceProvider`:

    Spark::noAdditionalTeams();

<a name="changing-how-spark-refers-to-teams"></a>
### Changing How Spark Refers To Teams

By default, Spark uses the "/teams/" segment in URIs to refer to teams. However, you may customize this using the `prefixTeamsAs` method in the `register` method of your `SparkServiceProvider`:

    Spark::prefixTeamsAs('bands');

> Be sure to call this method in the `register` method of your service provider, as Spark will not function correctly if it is called in the `boot` method. Additionally, make sure you pass the plural, lowercase form of the word.

To change the word used to refer to "teams" in views, you should update the `resources/lang/en/teams.php` [language file](/docs/11.0/localization).

<a name="accessing-the-users-teams"></a>
## Accessing The User's Teams

The `CanJoinTeams` trait provides several methods to assist you in accessing a given user's teams. The trait defines a `teams` relation to the `App\Team` model allows you to iterate over all of the user's teams:

    foreach ($user->teams as $team) {
        echo $team->name;
    }

The trait also provides several other helper methods that are useful when developing team based applications:

    if ($user->hasTeams()) {
        // This user belongs to at least one team...
    }

    if ($user->onTeam($team)) {
        // The user belongs to the given team...
    }

    if ($user->ownsTeam($team)) {
        // The user owns the given team...
    }

    foreach ($user->ownedTeams as $team) {
        // Iterate through all of the user's owned teams...
    }

    foreach ($team->invitations as $invitation) {
        // Iterate through all active invitations for the team...
    }

### The Current Team

When building team based applications in Spark, users will almost always belong to a "current team". This is the team that is currently selected from the team selection list from the user profile drop-down menu at the top right of the screen.

The team listing allows users to switch between team contexts in your application. So, for example, if you are building a helpdesk application, switching teams allows users to switch between team inboxes.

You may access the user's "current team" using the `currentTeam` property on a `App\User` instance:

    echo $user->currentTeam->name;

If the user does not belong to any team, the `currentTeam` property will be `null`, so you may wish to check that the property is not null before accessing it:

    if ($user->currentTeam) {
        //
    }

However, by default, when a user does not belong to any teams, they will be redirected to a warning notice informing them that they should create a team to continue using the application. To view this notice, simply register a test user, delete all of their teams, and then attempt to access the application `/home` URI. This redirection is provided by the `VerifyUserHasTeam` middleware.

<a name="accessing-teams-by-path"></a>
## Accessing Teams By Path

Spark also supports disabling the "team switcher" located in the top-right navigation menu and allowing you to have full customization of determining which team a user is currently viewing. To enable this option, call the `Spark::identifyTeamsByPath()` option in the `boot` method of your `SparkServiceProvider`:

    Spark::identifyTeamsByPath();

Once this option has been enabled, the team creation screen will include a new field where the user may choose a team "slug" that can be used in your application's route URIs to identify the team resources the user wishes to view. The team slug must be unique across your entire application for each team. Validation rules to ensure this uniqueness are already included in Spark. Once this option has been enabled you might define application routes like so in order to determine which team a user wishes to view:

    Route::get('/{team_slug}/projects', 'ProjectController@index');

<a name="team-roles"></a>
## Team Roles

Spark allows you to define roles for your team's members. By default, Spark has two roles: `owner` and `member`. However, you may define additional roles which the owner of a team can then assign to users from the team membership screen.

To define roles, call the `useRoles` method from the `boot` method of your `SparkServiceProvider`. The `useRoles` method accepts an array where the keys are the role "slugs" that will be stored in the database, while the values are the displayable name of the role:

    Spark::useRoles([
        'member' => 'Member',
        'vip' => 'VIP',
    ]);

If you would like to get the role that a user has for a given team, you may use the `roleOn` method on a user instance:

    if ($user->roleOn($team) === 'vip') {
        // This user is a VIP...
    }

You may also use the `ownsTeam` method to determine if a user owns a given team:

    if ($user->ownsTeam($team)) {
        // This user owns the team...
    }

The `ownedTeams` method returns all of the user's owned teams:

    foreach ($user->ownedTeams as $team) {
        echo $team->name;
    }

<a name="team-billing"></a>
## Team Billing

Team billing allows your Spark applications to provide billing plans on a per-team basis, meaning users may subscribe to different billing plans for each team they own. For more information on configuring team billing, please refer to the [billing documentation](/docs/11.0/billing#configuring-team-billing-plans)

<a name="using-teams-without-team-billing"></a>
## Using Teams Without Team Billing

If you would like to offer teams in your application but do not want to use team billing, Spark makes it a breeze. First, use the `Laravel\Spark\CanJoinTeams` trait on your `User` model:

    <?php

    namespace App;

    use Laravel\Spark\CanJoinTeams;
    use Laravel\Spark\User as SparkUser;

    class User extends SparkUser
    {
        use CanJoinTeams;
    }

If your application uses user billing and you want to limit the number of teams a given user can create while on a given plan, you can use the `maxTeams` method when defining the plan in your `SparkServiceProvider`:

    Spark::plan('Pro', 'yearly-pro')
        ->price(100)
        ->yearly()
        ->maxTeams(5);

<a name="team-events"></a>
## Team Events

Spark fires several team related events. These may be used to perform various actions when a user joins or leaves a team, such as  incrementing the subscription "quantity" if you are using Stripe to implement "per-user" pricing.

**All events listed in the following table are relative to the `Laravel\Spark\Events` namespace:**

Event  | Description
------------- | -------------
`Teams\DeletingTeam`  |  A team is about to be deleted. You may use this to remove all of the team's data.
`Teams\TeamCreated`  |  A team has been created.
`Teams\TeamDeleted`  |  A team has been deleted.
`Teams\TeamMemberAdded`  |  A new user has joined a team.
`Teams\TeamMemberRemoved`  |  A user has left or been removed from a team.
`Teams\UserInvitedToTeam`  |  A user has been invited to join a team.
`Teams\Subscription\SubscriptionCancelled`  |  A team's subscription has been cancelled.
`Teams\Subscription\SubscriptionUpdated`  |  A team's subscription has been updated.
`Teams\Subscription\TeamSubscribed`  |  A team has begun a new subscription.
