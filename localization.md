# Localization

- [Introduction](#introduction)
- [Translating the Terms of Service](#translating-terms-of-service)
- [Switching to a right-to-left layout](#switching-to-right-to-left-layout)

<a name="introduction"></a>
## Introduction

Spark utilizes the powerful translation component shipped with Laravel, by default Spark is shipped with an English interface and right-to-left layout.

You can add more languages by copying the `resources/lang/en.json` file into a new file with the language you prefer (e.g. `sp.json`), you'll also need to make a copy of `resources/lang/en/teams.php` with the translations of team-related strings.

<a name="translating-terms-of-service"></a>
## Translating the Terms of Service

To translate the Terms of Service file you need to create a `terms.[lang].md` file and provide your translated content. Spark will display the content of this file based on the current application language.

If no file with the required language were found, Spark will use the default `terms.md` file.

<a name="switching-to-right-to-left-layout"></a>
## Switching to a right-to-left layout

To switch your application to using a right-to-left layout you can simply add this to the `boot()` method of your SparkServiceProvider

```php
Spark::useRightToLeftTheme();
```

You can customize how the RTL layout looks like by adding your changes to `resources/assets/sass/app-rtl.scss`.