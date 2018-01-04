# Localization

- [Introduction](#introduction)
- [Translating The Terms Of Service](#translating-terms-of-service)
- [Right-To-Left Theme](#right-to-left-theme)

<a name="introduction"></a>
## Introduction

Spark utilizes the powerful translation component shipped with Laravel. By default, Spark is shipped with an English interface and a left-to-right theme.

You may easily support another language in your application by copying the `resources/lang/en.json` file into a new file named after the language you prefer (such as `sp.json`). You'll also need to copy the `resources/lang/en/teams.php` file, which contains team-related string translations, into a translation folder corresponding to your desired language.

<a name="translating-terms-of-service"></a>
## Translating The Terms Of Service

To translate your application's "Terms of Service" file, you should create a `terms.[lang].md` file and provide your translated content. For example, if you wish to support the Spanish language, you should create a `terms.sp.md` file. Spark will display the content of this file based on the application's configured language.

If a file with the requested language is not found, Spark will use the default `terms.md` file.

<a name="right-to-left-theme"></a>
## Right-To-Left Theme

If you would like your application to use a "right-to-left" theme, you should call the `useRightToLeftTheme` method in the `booted` method of your `SparkServiceProvider`:

    Spark::useRightToLeftTheme();

You may further customize how the right-to-left theme appears by modifying the `resources/assets/sass/app-rtl.scss` file and re-compiling your assets.
