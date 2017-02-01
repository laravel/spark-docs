# Adding Profile Fields

- [Introduction](#introduction)
- [Modifying The View](#modifying-the-view)
- [Defining The JavaScript Component](#defining-the-javascript-component)
- [Validation & Storage](#validation-and-storage)

<a name="introduction"></a>
## Introduction

Once you've added extra fields to the Spark registration form, you will most likely want to add them to the user's "profile" settings, allowing them to update the fields even after they have registered. In this overview, we'll take a look at how to add an extra field to the profile form and update its value in our database.

<a name="modifying-the-view"></a>
## Modifying The View

The "profile" tab of the settings dashboard contains several panels to update various information. We will add a new panel to this screen which will allow our users to update their age. First, let's add a Blade `@include` directive to the `/resources/views/vendor/spark/settings/profile.blade.php` file to load a new `update-profile-details` template:

    <spark-profile :user="user" inline-template>
        <div>
            <!-- Update Profile Photo -->
            @include('spark::settings.profile.update-profile-photo')

            <!-- Update Contact Information -->
            @include('spark::settings.profile.update-contact-information')

            <!-- Update Profile Details -->
            @include('settings.profile.update-profile-details')
        </div>
    </spark-profile>

This `@include` directive will load a new Blade template which contains our custom panel. We can define the new template at `/resources/views/settings/profile/update-profile-details`. This template will contain a simple form where the user can update their age:

    <update-profile-details :user="user" inline-template>
        <div class="panel panel-default">
            <div class="panel-heading">Profile Details</div>

            <div class="panel-body">
                <!-- Success Message -->
                <div class="alert alert-success" v-if="form.successful">
                    Your profile has been updated!
                </div>

                <form class="form-horizontal" role="form">
                    <!-- Age -->
                    <div class="form-group" :class="{'has-error': form.errors.has('age')}">
                        <label class="col-md-4 control-label">Age</label>

                        <div class="col-md-6">
                            <input type="text" class="form-control" name="age" v-model="form.age">

                            <span class="help-block" v-show="form.errors.has('age')">
                                @{{ form.errors.get('age') }}
                            </span>
                        </div>
                    </div>

                    <!-- Update Button -->
                    <div class="form-group">
                        <div class="col-md-offset-4 col-md-6">
                            <button type="submit" class="btn btn-primary"
                                    @click.prevent="update"
                                    :disabled="form.busy">

                                Update
                            </button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </update-profile-details>

<a name="defining-the-javascript-component"></a>
## Defining The JavaScript Component

Next, we will define the new `update-profile-details` Vue component which will manage our new panel and form. We can define this component at `/resources/assets/js/components/settings/profile/update-profile-details.js`:

    Vue.component('update-profile-details', {
        props: ['user'],

        data() {
            return {
                form: new SparkForm({
                    age: ''
                })
            };
        },

        mounted() {
            this.form.age = this.user.age;
        },

        methods: {
            update() {
                Spark.put('/settings/profile/details', this.form)
                    .then(response => {
                        Bus.$emit('updateUser');
                    });
            }
        }
    });

Note that this component uses the `Spark.put` helper, which adds some convenient features on top of the `axios` library. The `Spark` HTTP helpers accept a form object and will automatically the form's `busy` attribute to `true` when the form is submitted. The helper will also automatically handle any validation errors that are returned from the server.

When you define a new Vue component, you also need to instruct Spark to compile the component into your main `app.js` file. You can do this by adding a line to your `/resources/assets/js/components/bootstrap.js` file:

    require('./home');

    // Load the new Vue component...
    require('./settings/profile/update-profile-details');

> **Note:** After defining the new Vue component, remember to run the `gulp` command in your terminal to re-compile your JavaScript.

<a name="validation-and-storage"></a>
## Validation & Storage

Next, we need to define a route and controller to handle updating the `age` for an existing user. First, let's add a route to our `routes/web.php` file:

    Route::put('/settings/profile/details', 'ProfileDetailsController@update');

Once the route has been defined, we can define the controller in `app/Http/Controllers/ProfileDetailsController.php`:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class ProfileDetailsController extends Controller
    {
        /**
         * Create a new controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->middleware('auth');
        }

        /**
         * Update the user's profile details.
         *
         * @return Response
         */
        public function update(Request $request)
        {
            $this->validate($request, [
                'age' => 'required|integer|min:1',
            ]);

            $request->user()->forceFill([
                'age' => $request->age
            ])->save();
        }
    }

Finally, we're ready to visit the "profile" settings screen and update our age! This same general process can be applied to customizing most of the other "settings" screens within Spark, so feel free to customize any of these screens to meet the needs of your application.
