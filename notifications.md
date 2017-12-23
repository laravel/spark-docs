# Notifications

- [Introduction](#introduction)
- [Creating Notifications](#creating-notifications)
- [Sending Laravel 5 Notifications](#sending-laravel-5-notifications)

<a name="introduction"></a>
## Introduction

Notifications are a great way to inform your users of things that are happening in your application. For example, you might use a notification to let a user know when a team member has completed a given to-do list task. These notifications are viewable by clicking on the "notification bell" icon in the application's navigation bar. The notification bell will have a red activity indicator when there are unread announcements or notifications.

<a name="creating-notifications"></a>
## Creating Notifications

To create a notification, inject the `Laravel\Spark\Contracts\Repositories\NotificationRepository` into your controller or any other class which is being resolved by the Laravel service container:

    use Laravel\Spark\Contracts\Repositories\NotificationRepository;

    public function __construct(NotificationRepository $notifications)
    {
        $this->notifications = $notifications;
    }

Then, you may use the `create` method to create the notification:

    $this->notifications->create($user, [
        'icon' => 'fa-users',
        'body' => 'A team member completed a task!',
        'action_text' => 'View Task',
        'action_url' => '/link/to/task',
    ]);

The `action_text` and `action_url` attributes are optional so you do not need to pass them in order to create a notification. However, providing these attributes will create a button on the notification where the user can be directed to some other location with information relevant to the notification.

When a notification is created, Spark will fire the `Laravel\Spark\Events\NotificationCreated` event, which your application can listen for to do work after a notification is created.

<a name="sending-laravel-53-notifications"></a>
## Sending Laravel 5 Notifications

Spark also allows you to send notifications using the Laravel 5 notification system. To create a notification, you may use the following Artisan command:

```
php artisan make:notification TaskCompleted
```

This command will place a fresh notification in your `app/Notifications` directory. You may add the notification to Spark's notification table by specifying the `SparkChannel` channel in your notification's `via` method. The `SparkChannel` may be thought of as a replacement to Laravel's default `database` channel:

```php
use Laravel\Spark\Notifications\SparkChannel;

public function via($notifiable)
{
    return [SparkChannel::class];
}
```

### Formatting Notifications

Before using the `SparkChannel`, you should define a `toSpark` method on the notification class. This method will receive a `$notifiable` entity and should return a SparkNotification object:

```php
use Laravel\Spark\Notifications\SparkNotification;

public function toSpark($notifiable)
{
    return (new SparkNotification)
                  ->action('View Task', '/link/to/task')
                  ->icon('fa-users')
                  ->body('A team member completed a task!');
}
```

### Sending Notifications

Notifications may be sent in two ways: using the `notify` method of the `Notifiable` trait or by using the `Notification` facade. You may take a look at the Laravel [notification documentation](https://laravel.com/docs/5.3/notifications#sending-notifications) to get more details regarding these two approaches to sending notifications.

Here's an example of sending notifications to a Spark user using the `notify` method:

```php
$user->notify(new TaskCompleted);
```

When sending a notification to a team, all members of that team will be notified:

```php
$team->notify(new TaskCompleted);
```
