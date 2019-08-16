# Announcements

- [Introduction](#introduction)
- [Announcement Events](#announcement-events)

<a name="introduction"></a>
## Introduction

From the [Spark Kiosk](/docs/7.0/kiosk), you may create announcements to be displayed to your users. These announcements may be viewed by users when they click on the "notification bell" icon in the application's navigation bar. The notification bell will have an unread count indicator when there are unread announcements or notifications.

To create an announcement, [configure your user as a developer](/docs/7.0/kiosk) and enter the Kiosk via the user profile drop-down menu, then click the "Announcements" tab.

The announcement text supports Markdown, so feel free to use Markdown syntax within your announcement body. If needed, you may attach an "Action Button" to the announcement. Adding an action button gives users a chance to view more information about the announcement, such as a blog post.

<a name="announcement-events"></a>
## Announcement Events

When an announcement is created, the `Laravel\Spark\Events\AnnouncementCreated` event is dispatched, allowing you to perform additional actions such as sending an announcement push notification, etc.
