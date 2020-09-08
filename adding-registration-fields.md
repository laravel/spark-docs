# Adding Registration Fields

- [Introduction](#introduction)
- [Modifying The View](#modifying-the-view)
- [Extending The JavaScript](#extending-the-javascript)
- [Validation & Storage](#validation-and-storage)

<a name="introduction"></a>
## Introduction

A common Spark extension use case is adding fields to the Spark registration form. In this overview, we'll take a look at how to add an extra field to the registration process and store its value in our database.

<a name="modifying-the-view"></a>
## Modifying The View

First, let's add a new `age` form field to the `/resources/views/vendor/spark/auth/register-common-form.blade.php` file. We'll place this new field under the existing "e-mail" field:

    <!-- Age -->
    <div class="form-group row">
        <label class="col-md-4 col-form-label text-md-right">Age</label>

        <div class="col-md-6">
            <input type="text" class="form-control" name="age"
                   v-model="registerForm.age"
                   :class="{'is-invalid': registerForm.errors.has('age')}">

            <span class="invalid-feedback" v-show="registerForm.errors.has('age')">
                @{{ registerForm.errors.get('age') }}
            </span>
        </div>
    </div>

Remember, you're free to edit any of the views in `/resources/views/vendor/spark`. When you update Spark, any of the views you haven't modified will automatically be updated. If you have modified a view, you will be notified that it was not replaced in order to preserve your changes.

<a name="extending-the-javascript"></a>
## Extending The JavaScript

Since extending the registration form is a common use case, Spark makes it easy to add a field to the JavaScript object that models the registration form. In your `/resources/js/app.js` file, adding the following lines of code just before the call to `new Vue`:

    Spark.forms.register = {
        age: ''
    };

    var app = new Vue({
        mixins: [require('spark')]
    });

The contents of `Spark.form.register` will automatically be merged with the base definition of the registration form.

<a name="validation-and-storage"></a>
## Validation & Storage

Next, we need to customize Spark's new user validation and storage methods to handle our new `age` field. Within the `boot` method of our `SparkServiceProvider`, we'll call the `validateUsersWith` and `createUsersWith` methods to customize this logic. First, let's call `validateUsersWith` and define the validation rules for our new `age` field:

    Spark::validateUsersWith(function () {
        return [
            'name' => 'required|max:255',
            'age' => 'required|integer|min:1',
            'email' => 'required|email|max:255|unique:users',
            'password' => 'required|confirmed|min:6',
            'vat_id' => 'max:50|vat_id',
            'terms' => 'required|accepted',
        ];
    });

If you do not want to pass a Closure, you may also pass a `Class@method` string to instruct Spark which class and method to call in order to obtain the validation rules:

    Spark::validateUsersWith('UserValidator@validate');

Next, let's call `createUsersWith` to make sure our new field is stored in the database:

    Spark::createUsersWith(function ($request) {
        $user = Spark::user();

        $data = $request->all();

        $user->forceFill([
            'name' => $data['name'],
            'email' => $data['email'],
            'age' => $data['age'],
            'password' => bcrypt($data['password']),
            'last_read_announcements_at' => Carbon::now(),
            'trial_ends_at' => Carbon::now()->addDays(Spark::trialDays()),
        ])->save();

        return $user;
    });

As was the case with validation, you may also pass a `Class@method` callback:

    Spark::createUsersWith('UserCreator@create');
