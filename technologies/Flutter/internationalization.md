# Internacionalização no Flutter

Para adicionar suporte à internacionalização no seu aplicativo Flutter, siga os passos abaixo:

## 1. Adicione Dependências

Abra o arquivo `pubspec.yaml` e inclua `flutter_localizations` nas dependências de desenvolvimento:

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
```

Depois, execute o comando a seguir para instalar as dependências:

```bash
flutter pub get
```

## 2. Habilite a Geração de Traduções

Para que o Flutter possa gerar automaticamente o código de tradução a partir dos arquivos .arb, precisa que seja habilitado a geração de código. Isso é feito no arquivo pubspec.yaml, onde adiciona a configuração `generate: true`.

Para habilitar esse recurso, abra o arquivo pubspec.yaml do seu projeto, e no bddloco de configuração do Flutter (flutter), adicione a linha:

```yaml
flutter:
  uses-material-design: true
  generate: true
```

## 3. Crie o Arquivo de Configuração

Na raiz do seu projeto, crie um arquivo chamado `l10n.yaml`. Nele insira as seguintes configurações:

```yaml
arb-dir: lib\l10n
template-arb-file: app_en.arb
output-localization-file: app_localizations.dart
```

- **arb-dir**: Define o diretório onde os arquivos `.arb` (Application Resource Bundle) serão armazenados.
- **template-arb-file**: Especifica o arquivo de modelo que servirá de base para as traduções.
- **output-localization-file**: Define o nome do arquivo onde as traduções geradas serão salvas.

## 4. Criação das Traduções

A criação das traduções no Flutter envolve a definição dos idiomas suportados, a criação de arquivos de recursos `.arb` contendo as traduções específicas de cada idioma e a correta configuração do sistema de internacionalização.

### 1. Criação do Arquivo de Configuração de Idiomas

É necessário criar um arquivo responsável pela gestão dos idiomas disponíveis no aplicativo, da seguinte maneira:

- Criar uma subpasta chamada `l10n` dentro da pasta `lib`.
- Dentro da pasta `l10n`, criar um arquivo chamado `l10n.dart` com o seguinte conteúdo:

```dart
import 'dart:ui';

class L10n {
  static final all = <Locale>[
    const Locale('en', ''),   // Inglês
    const Locale('pt', 'BR'), // Português (Brasil)
    // Outros idiomas
  ];
}
```

No código acima, a lista `all` contém os idiomas que o aplicativo terá suporte. A chave `Locale` é composta por dois parâmetros: o código do idioma (como `en` para inglês e `pt` para português) e, opcionalmente, um subtipo para variantes do idioma (como `BR` para português do Brasil).

### 2. Criação dos Arquivos `.arb`

Os arquivos `.arb` (Application Resource Bundle) são usados para armazenar as traduções dos textos do aplicativo em diferentes idiomas. Cada idioma terá um arquivo `.arb` correspondente, e o nome desses arquivos deve seguir o padrão `app_[código do idioma].arb`, como `app_en.arb` para Inglês, `app_pt.arb` para português, e assim por diante.

Estrutura do Arquivo

Cada arquivo `.arb` deve conter a chave `@@locale`, que especifica o idioma do arquivo, como:

- Inglês (app_en.arb):

```json
{
  "@@locale": "en",
  "title": "Title",
  "description": "Description"
}
```

- Português (app_pt.arb):

```json
{
  "@@locale": "pt",
  "title": "Título",
  "description": "Descrição"
}
```

- Português do Brasil (app_pt_BR.arb):

```json
{
  "@@locale": "pt_BR",
  "title": "Título",
  "description": "Descrição"
}
```

### 3. Definição das Chaves de Tradução

Dentro de cada arquivo `.arb`, as traduções são organizadas utilizando pares de chave e valor. A chave representa o identificador de um texto traduzido, enquanto o valor contém a tradução correspondente para o idioma do arquivo.

As chaves devem ser únicas e descritivas, de modo a refletir claramente o texto que está sendo traduzido. Assim como idênticas em todos os arquivos `.arb`, se uma chave chamada "title" for utilizada em `app_en.arb`, ela deverá ser a mesma para `app_pt.arb` e `app_pt_BR.arb`.

### 4. Variantes de Idioma

Em algumas situações será necessário criar variantes de um idioma, como no caso do Português, que pode ter variações regionais (como o Português do Brasil). Quando isso ocorrer, será necessário criar arquivos `.arb` distintos para cada variante.

O Flutter gerencia automaticamente as variantes de idioma. Caso uma tradução para uma variante específica não esteja disponível, o sistema utilizará a tradução do idioma base (como pt), garantindo que, mesmo na ausência de uma tradução específica, o texto será exibido corretamente, fazendo uso da tradução do idioma base.

## 5. Gere as Traduções

Após a criação dos arquivos `.arb`, é necessário recarregar a aplicação para que o Flutter gere o código de tradução automaticamente.

## Aplicação das Traduções

Para integrar e aplicar as traduções, siga os passos abaixo:

### 1. Aplicar `AppLocalizations`

Primeiramente, importe o pacote `AppLocalizations` no arquivo onde o `MaterialApp` está configurado:

```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
```

No `MaterialApp`, será adicionado as configurações necessárias para suportar a internacionalização e o gerenciamento dinâmico de idiomas. É importante destacar que a escolha do idioma do aplicativo pode ser dinâmica, permitindo que o idioma seja alterado conforme as configurações do sistema ou a seleção do usuário. Para isso, o `locale` do `MaterialApp` deve ser configurado de forma que possa ser alterado em tempo de execução.

```dart
MaterialApp(
  debugShowCheckedModeBanner: false,
  supportedLocales: L10n.all, // Define os idiomas suportados
  locale: Locale('en'), // Idioma padrão. O valor pode ser dinâmico
  localizationsDelegates: <LocalizationsDelegate>[
    AppLocalizations.delegate, // Responsável pelas traduções
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
    GlobalCupertinoLocalizations.delegate,
  ],
  home: HomeScreen(),
),
```

### 2. Alteração Dinâmica do Idioma

Para permitir que o idioma do aplicativo seja alterado durante a execução, pode usar um controlador ou notificador, como o `ValueNotifier`, gerenciando a alteração do `locale` no `MaterialApp`. Esse método permite que a alteração do idioma seja aplicada em tempo real, sem a necessidade de reiniciar o aplicativo.

Exemplo de como implementar:

```dart
final _languageNotifier = ValueNotifier<String>('en'); // Idioma padrão: inglês

MaterialApp(
  debugShowCheckedModeBanner: false,
  supportedLocales: L10n.all,
  locale: Locale(_languageNotifier.value), // A língua é dinâmica
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
```

### 3. Usar as Traduções

Sempre que precisar usar as traduções, certifique-se de importar o `AppLocalizations`:

```dart
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
```

Em seguida, para acessar as traduções, use:

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
