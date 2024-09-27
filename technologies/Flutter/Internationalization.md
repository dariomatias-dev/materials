# Internacionalização

Siga os passos abaixo para adicionar internacionalização no seu aplicativo Flutter:

Abra `pubspec.yaml` e adicione `flutter_localizations` nas dependências de desenvolvimento:

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

Na raiz do seu projeto Flutter, crie um arquivo chamado `l10n.yaml`.

No arquivo `l10n.yaml`, insira o seguinte código:

```yaml
arb-dir: lib/l10n
template-arb-file: app_en.arb
output-localization-file: app_localizations.dart
```

Em `arb-dir` é definido onde os arquivos `.arb` (Application Resource Bundle) estão armazenados.
Na chave `template-arb-file` será especificado o arquivo de template (modelo) que será usado como base das traduções.
A chave `output-localization-file` será para definir o nome do arquivo de saída para as traduções geradas.

## Criação das Traduções

Dentro da pasta `lib` crie uma pasta com o nome `l10n`, com o seguinte código:

```dart
import 'dart:ui';

class L10n {
  static final all = <Locale>[
    const Locale('en', ''),
    const Locale('pt', 'BR'),
  ];
}
```

Dentro da lista cologue todos os idiomas que serão suportados.

Na pasta `l10n` crie arquivos com o seguinte padrão `app_[código do idioma].arb`, e cologue:
```json
{
    "@@locale": "en",
}
```

No campo `locale` ficará o idioma.

Crie todos os arquivos de tradução que serão usados.
Para as traduções, todas devem ter a mesma chave, como `description`, seguida da tradução para o respectivo idioma, como `Description` para Inglês, e `Descrição` para Português do Brasil.

Faça o reload da aplicação para as traduções serem geradas.

## Aplicação das Traduções

Em `MaterialApp`, cologue o seguinte:

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

Para usar as traduções:

```dart
AppLocalizations.of(context)!.[nome da chave],
```
