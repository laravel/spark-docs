# Client Customization Primer

- [Introduction](#introduction)
- [Views](#views)
- [Adding Your Own Vue Components](#adding-your-own-vue-components)
- [Customizing Existing Vue Components](#customizing-existing-vue-components)
- [Using Something Other Than Vue](#other-than-vue)

<a name="introduction"></a>
## Introduction

Spark makes it simple to modify the Spark view templates, supplement your own Vue components, and customize the existing Vue components. In this primer we'll cover all of these subjects to help you get started making Spark your own.

<a name="views"></a>
## Views

When you create a new application using `spark new`, all of the Spark views are automatically exported to your application's `resources/views/vendor/spark` directory. You may customize any of these views as needed.

When you upgrade your application using `php artisan spark:update`, any views that have been modified will not be updated. Instead, you will simply be notified that these views were skipped.

<a name="adding-your-own-vue-components"></a>
## Adding Your Own Vue Components

When you install Spark, a `resources/assets/js/components` directory is created to hold all of your own Vue components. To learn more about writing Vue components, be sure to check out the [Vue documentation](http://vuejs.org/guide/components.html). A sample `home` component is included with Spark and can serve as the main "dashboard" for your application. The corresponding view for this component is located at `resources/views/home.blade.php`.

For an example of an application with custom Vue components, check out the Spark [demo application](/docs/3.0/quickstart#demo-application).

> **Note:** When you add additional components to your application, don't forget to `require` them in your `resources/assets/js/app.js` file.

<a name="customizing-existing-vue-components"></a>
## Customizing Existing Vue Components

When Spark is installed, a `resources/assets/js/spark-components` directory will be created which contains stubs for every Vue JavaScript component included with Spark. If you open one of these components, you will notice that each of them is an empty component definition except for the `base` mixin of the component which provides the Spark core functionality.

Every component in this directory leverages Vue's [mixin](http://vuejs.org/guide/mixins.html) feature, meaning you can override any of the methods defined in the base Spark component.

For example, if you wish to override the `update-contact-information` component's `update` method, you would define the following method in the `resources/assets/js/spark-components/settings/profile/update-contact-information.js` file:

    var base = require('settings/profile/update-contact-information');

    Vue.component('spark-update-contact-information', {
        mixins: [base],

        methods: {
            update: function () {
                console.log('My Custom Method!');
            }
        }
    });

Of course, once you customize a component, don't forget to run the `gulp` command to re-compile your JavaScript. Otherwise, your changes will not take effect.

<a name="other-than-vue"></a>
## Using Something Other Than Vue

Using Vue is required for the registration and settings views; however, you may use any JavaScript framework you want for the "bulk" for your application outside of these views.

> **Note:** Vue is required for the following features: notifications, support requests, and automatic handling of [shared API back-ends](/docs/3.0/api#sharing-your-api).

To use something other than Vue, modify your `resources/views/home.blade.php` view to extend the `spark::layouts.blade.app` template. This layout does not contain any JavaScript framework initialization and the Vue application will not be attached to the page, leaving you free to build your JavaScript client however you wish in your `resources/assets/js/app.js` file. However, a few utility libraries such as Underscore, Moment, and jQuery are still loaded even when Vue is disabled for your application.

Next, remove the following line from your `resources/assets/js/components/bootstrap.js` file:

    require('./../spark-components/bootstrap');

Finally, you may remove the Vue app instantiation from the `resources/assets/js/app.js` file:

    var app = new Vue({
        mixins: [require('spark')]
    });
