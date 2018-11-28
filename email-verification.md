# Email Verification

Spark 7.1 supports email verification out of the box. You can configure spark to send verification emails to newly registered users that contain a link to verify their ownership.

To enable email verification, verify that your `App\User` model implements the `Illuminate\Contracts\Auth\MustVerifyEmail` contract.

By default, Spark will not prevent newly registered users from accessing their account before they verify their email addresses. However, you can configure spark to redirect users to a notification screen to enforce email verification. To do this you need to add the following to the `register()` method of your SparkServerProvider:

    Spark::ensureEmailIsVerified();