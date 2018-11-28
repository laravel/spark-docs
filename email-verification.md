# Email Verification

Beginning with Spark 7.1, you may use Laravel 5.7's email verification services out of the box. This will ensure Spark sends verification emails to newly registered users that contain a link to verify their ownership of the email address.

To enable email verification, verify that your `App\User` model implements the `Illuminate\Contracts\Auth\MustVerifyEmail` contract.

By default, Spark will not prevent newly registered users from accessing their account before they verify their email addresses. However, you can configure Spark to redirect unverified users to a prompt screen that informs them of the need to verify their email. To do this, add the following to the `register` method of your `SparkServerProvider`:

    Spark::ensureEmailIsVerified();
