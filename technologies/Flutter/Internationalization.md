# Internacionalização

Siga os passos abaixo para adicionar internacionalização no seu aplicativo Flutter:

Abra o arquivo `pubspec.yaml` e adicione `flutter_localizations` nas dependências de desenvolvimento:

```bash
dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
```

Agora rode `flutter pub get` para obter as dependências.

Em `flutter`, adicione `generate: true` para permitir a geração das traduções:

```bash
flutter:
  uses-material-design: true
  generate: true
```

Na raiz do projeto, crie um arquivo chamado `l10n.yaml`.

No arquivo `l10n.yaml`, insira as seguintes configurações:

```yaml
arb-dir: lib/l10n
template-arb-file: app_en.arb
output-localization-file: app_localizations.dart
```

Em `arb-dir` é definido onde os arquivos `.arb` (Application Resource Bundle) estão armazenados.
Na chave `template-arb-file` será especificado o arquivo de template (modelo) que será usado como base das traduções.
A chave `output-localization-file` será para definir o nome do arquivo de saída para as traduções geradas.

## Criação das Traduções

Na pasta `lib` crie uma pasta com o nome `l10n`, e um arquivo `l10n.dart`, com o seguinte código:

```dart
import 'dart:ui';

class L10n {
  static final all = <Locale>[
    const Locale('en', ''),
    const Locale('pt', 'BR'),
  ];
}
```

Dentro dessa lista deve ser colocado todos os idiomas que serão suportados.

Em `l10n` ficará os arquivos com as traduções, que seguem o padrão de nomeação: `app_[código do idioma].arb`.
Em todos os arquivos deve ser definido o idioma das traduções, como:

```json
{
    "@@locale": "en",
}
```

Em todos os arquivo deve ser usado a mesma chave para se referir a mesma tradução:

1 - Inglês:

```json
{
    "@@locale": "en",
    "title": "Title",
    "description": "Description"
}
```

2 - Português do Brasil:

```json
{
    "@@locale": "pt_BR",
    "title": "Título",
    "description": "Descrição"
}
```

Faça o reload da aplicação para as traduções serem geradas.

## Aplicação das Traduções

Para usar as traduções, siga os passos abaixo:

Nas importações cologue:
```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
```

E cole o seguinte código em `MaterialApp`:

```dart
supportedLocales: L10n.all,
locale: Locale('en'),
localizationsDelegates: <LocalizationsDelegate>[
AppLocalizations.delegate,
GlobalMaterialLocalizations.delegate,
GlobalWidgetsLocalizations.delegate,
GlobalCupertinoLocalizations.delegate,
],
```

Código completo:

```dart
MaterialApp(
    debugShowCheckedModeBanner: false,
    supportedLocales: L10n.all,
    locale: Locale('en'), // Idioma do aplicativo. Deve ser dinâmico para permitir a mudança de idioma.
    localizationsDelegates: <LocalizationsDelegate>[
    AppLocalizations.delegate,
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
    GlobalCupertinoLocalizations.delegate,
    ],
    home: HomeScreen(),
),
```

Para usar as traduções:

Sempre cologue a importação do `AppLocalizations` onde for usar as traduções:

```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
```

E o seguinte para usar as traduções:

```dart
AppLocalizations.of(context)!.[nome da chave],
```

## Exemplo de Aplicativo

Logo abaixo terá um exemplo da aplicação da internacionalização, com mudança de idioma dinâmico.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_localizations/flutter_localizations.dart';

import 'package:flutter_app/l10n/l10n.dart';

import 'package:flutter_gen/gen_l10n/app_localizations.dart';

class Languages {
  static const en = 'en';
  static const ptBr = 'pt_BR';

  static Locale getLocale(
    String language,
  ) {
    switch (language) {
      case ptBr:
        return Locale('pt', 'BR');
      default:
        return Locale('en');
    }
  }
}

class LanguageModel {
  const LanguageModel({
    required this.name,
    required this.code,
  });

  final String name;
  final String code;
}

const languages = <LanguageModel>[
  LanguageModel(
    name: 'Português - Brasil',
    code: Languages.ptBr,
  ),
  LanguageModel(
    name: 'English',
    code: Languages.en,
  ),
];

void main() {
  runApp(const MyApp());
}

class MyApp extends StatefulWidget {
  const MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  final _languageNotifier = ValueNotifier(Languages.ptBr);

  @override
  Widget build(BuildContext context) {
    return ValueListenableBuilder(
      valueListenable: _languageNotifier,
      builder: (context, value, child) {
        return MaterialApp(
          debugShowCheckedModeBanner: false,
          supportedLocales: L10n.all,
          locale: Languages.getLocale(value),
          localizationsDelegates: <LocalizationsDelegate>[
            AppLocalizations.delegate,
            GlobalMaterialLocalizations.delegate,
            GlobalWidgetsLocalizations.delegate,
            GlobalCupertinoLocalizations.delegate,
          ],
          home: HomeScreen(
            languageNotifier: _languageNotifier,
          ),
        );
      },
    );
  }
}

class HomeScreen extends StatelessWidget {
  const HomeScreen({
    super.key,
    required this.languageNotifier,
  });

  final ValueNotifier<String> languageNotifier;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              AppLocalizations.of(context)!.title,
              style: TextStyle(
                fontSize: 18.0,
                fontWeight: FontWeight.w500,
              ),
            ),
            Text(
              AppLocalizations.of(context)!.description,
            ),
            SizedBox(height: 12.0),
            DropdownButtonHideUnderline(
              child: DropdownButton(
                value: languageNotifier.value,
                items: List.generate(
                  languages.length,
                  (index) {
                    final language = languages[index];

                    return DropdownMenuItem(
                      value: language.code,
                      child: Text(language.name),
                    );
                  },
                ),
                onChanged: (value) {
                  languageNotifier.value = value!;
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```
