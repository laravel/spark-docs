# Announcements

- [Introduction](#introduction)
- [Announcement Events](#announcement-events)

<a name="introduction"></a>
## Introduction

From the Spark Kiosk, you may create announcements to be displayed to your users. These announcements may be viewed by users by clicking on the "notification bell" icon in the application's navigation bar. The notification bell will have a red activity indicator when there are unread announcements or notifications.

To create an announcement, simply [configure your user as a developer](/docs/1.0/kiosk) and enter the Kiosk via the user profile drop-down menu, then click the "Announcements" tab.

The announcement text supports Markdown, so feel free to use Markdown syntax within your announcement body. You may optionally attach an "Action Button" to the announcement. Adding an action button gives users a chance to view more information about the announcement, such as a blog post.

<a name="announcement-events"></a>
## Announcement Events

When an announcement is created, the `Laravel\Spark\Events\Kiosk\AnnouncementCreated` event is fired, allowing you to perform additional actions such as sending an announcement push notification, etc.
