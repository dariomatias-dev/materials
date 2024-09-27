# Internacionalização no Flutter

Para adicionar suporte à internacionalização no seu aplicativo Flutter, siga os passos abaixo:

## 1. Adicione Dependências

Abra o arquivo `pubspec.yaml` e inclua `flutter_localizations` nas dependências de desenvolvimento:

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
```

Depois, execute o comando a seguir para instalar as dependências:

```bash
flutter pub get
```

## 2. Habilite Geração de Traduções

Em `pubspec.yaml`, adicione `generate: true` para permitir a geração automática das traduções:

```yaml
flutter:
  uses-material-design: true
  generate: true
```

## 3. Crie o Arquivo de Configuração

Na raiz do seu projeto, crie um arquivo chamado `l10n.yaml`. Insira as seguintes configurações nele:

```yaml
arb-dir: lib/l10n
template-arb-file: app_en.arb
output-localization-file: app_localizations.dart
```

- **arb-dir**: Define o diretório onde os arquivos `.arb` (Application Resource Bundle) serão armazenados.
- **template-arb-file**: Especifica o arquivo de modelo que servirá de base para as traduções.
- **output-localization-file**: Define o nome do arquivo onde as traduções geradas serão salvas.

## 4. Criação das Traduções

1. Na pasta `lib`, crie uma subpasta chamada `l10n` e adicione um arquivo `l10n.dart` com o seguinte código:

```dart
import 'dart:ui';

class L10n {
  static final all = <Locale>[
    const Locale('en', ''),
    const Locale('pt', 'BR'),
  ];
}
```

Nesta lista, adicione todos os idiomas que você deseja suportar.

2. Na pasta `l10n`, crie arquivos para cada idioma seguindo o padrão de nomeação `app_[código do idioma].arb`. Em cada arquivo, defina o idioma das traduções assim:

```json
{
    "@@locale": "en"
}
```

### Exemplo de Traduções

- **Inglês (`app_en.arb`)**:

```json
{
    "@@locale": "en",
    "title": "Title",
    "description": "Description"
}
```

- **Português do Brasil (`app_pt_BR.arb`)**:

```json
{
    "@@locale": "pt_BR",
    "title": "Título",
    "description": "Descrição"
}
```

## 5. Gere as Traduções

Após criar os arquivos `.arb`, faça um reload da aplicação para que as traduções sejam geradas.

## Aplicação das Traduções

Para integrar as traduções no seu aplicativo Flutter, siga os passos abaixo:

### 1. Aplicar `AppLocalizations`

Abra o arquivo onde está `MaterialApp`, e adicione a seguinte importação:

```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
```

No `MaterialApp`, adicione as configurações para suportar a internacionalização:

```dart
MaterialApp(
  debugShowCheckedModeBanner: false,
  supportedLocales: L10n.all,
  locale: Locale('en'), // Idioma padrão do aplicativo. Deve ser dinâmico para permitir mudanças.
  localizationsDelegates: <LocalizationsDelegate>[
    AppLocalizations.delegate,
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
    GlobalCupertinoLocalizations.delegate,
  ],
  home: HomeScreen(),
),
```

### 3. Usar as Traduções

Sempre que precisar usar as traduções, certifique-se de importar o `AppLocalizations`:

```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
```

Para acessar as traduções, use:

```dart
AppLocalizations.of(context)!.nomeDaChave;
```

## Exemplo Completo

Logo abaixo está um exemplo completo de como integrar e utilizar as traduções:

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
