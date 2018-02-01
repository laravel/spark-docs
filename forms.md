# Forms

- [Introduction](#introduction)
- [Usage](#usage)

<a name="introduction"></a>
## Introduction

Spark includes a helper class for working with form errors in your JavaScript application. You are not required to use these helpers but may find them useful when building the forms for your application.

<a name="usage"></a>
## Usage

#### Defining A Form

In your Vue component, you may define a form by instantiating a new instance of the `SparkForm` class. This would be defined in the `data` method of your component, which contains the entire data definition for your Vue component:

    Vue.component('example', {
        data() {
            return {
                form: new SparkForm({
                    name: '',
                    age: ''
                })
            };
        }
    });

#### Submitting A Form

The `SparkForm` class provides a few helpful properties such as `busy`, `successful`, and `errors` which let you inspect the state of the form. The `busy` property will be set automatically when the form is sent to the server using the `post`, `put`, or `delete` methods. This allows you to easily disable the "submit" button when the form is submitted:

    <button class="btn btn-primary" @click="register" :disabled="form.busy">
        Something
    </button>

Then, you may submit the form from your Vue component:

    methods: {
        register() {
            Spark.post('/example', this.form)
                .then(response => {
                    console.log(response);
                });
        }
    }

#### Displaying Validation Errors

You may use the `errors` property of the form to retrieve and display validation errors:

    <div class="form-group row">
        <label class="col-md-4 control-label">Name</label>

        <div class="col-md-6">
            <input type="text" class="form-control" name="name" v-model="form.name" :class="{'is-invalid': form.errors.has('name')}">

            <span class="invalid-feedback" v-show="form.errors.has('name')">
                @{{ form.errors.get('name') }}
            </span>
        </div>
    </div>

#### Displaying Success Messages

You may use the `successful` property to determine when to show a success message:

    <div class="alert alert-success" v-if="form.successful">
        Your contact information has been updated!
    </div>
