# Flutter

Package: `azbox` on pub.dev. Checked against version 1.0.16.

```yaml
dependencies:
  azbox: ^1.0.16
```

## Initialise

```dart
import 'package:flutter/material.dart';
import 'package:azbox/azbox.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Azbox.ensureInitialized(
    apiKey: const String.fromEnvironment('AZBOX_API_KEY'),
    projectId: const String.fromEnvironment('AZBOX_PROJECT_ID'),
  );
  runApp(Azbox(child: const MyApp()));
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      localizationsDelegates: context.localizationDelegates,
      supportedLocales: context.supportedLocales,
      locale: context.locale,
      home: const HomePage(),
    );
  }
}
```

Pass the values at build time instead of hard-coding them:

```bash
flutter run --dart-define=AZBOX_API_KEY=... --dart-define=AZBOX_PROJECT_ID=...
```

A key compiled into a mobile or web app can be extracted by anyone who has the app. Use a
project-scoped key from **Settings → API keys** so it can only read that project and can
be revoked.

`Azbox` widget options: `startLocale`, `saveLocale` (default `true`),
`useFallbackTranslations` (default `false`), `errorWidget`.

## Read a string

```dart
Text('home.title'.translate())
Text('home.title').translate()        // Text extension
context.translate('home.title')       // BuildContext extension
```

There is no `.tr()` method.

**`translate()` capitalizes the first letter by default** (`capitalize: true` on the
String extension). Pass `capitalize: false` when the text must keep its case, for example
a sentence fragment or a brand name.

## Arguments

```dart
'cart.items'.translate(args: ['3'])                          // "{} items"
'greeting'.translate(namedArgs: {'name': userName})          // "Hello {name}"
'welcome'.translate(gender: isFemale ? 'female' : 'male')     // gender map
'key'.trExists()                                             // does the key exist
```

`args` fills `{}` left to right; `namedArgs` fills `{name}`. There is no plural helper:
use separate keys (`cart.items_one`, `cart.items_other`) and choose in code.

## Locale

```dart
await context.setLocale(const Locale('es'));
context.locale;            // current locale
context.supportedLocales;  // from the languages configured in the AZbox project
await context.resetLocale();
```

Supported locales come from the project's languages, so adding a language in the
dashboard adds it to the app without a release.

## Over-the-air updates

Translations are fetched from the API and cached on the device, so text edited in the
dashboard reaches the app without a new build. The package caches keys for **24 hours**
and the list of languages for 1 hour, so a change can take up to a day to show in an app
that already has them cached. A string that renders as its key means the
key is missing in the project or has no text in that language.

## Adding strings

Keys are not created from the app. Add new keys to the project's ARB file (if there is
one) and ask the user to import it in the dashboard, or to add them there directly.
