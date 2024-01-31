# Abrir URL no Flutter

Abrir uma URL apertando em um botão, texto ou um outro widget, é uma necessidade muito comum em vários aplicativos, seja para redirecionar o usuário para o site oficial, fórum online, documentação ou mesmo os termos de uso.

Esse é um recurso que não vem junto ao instalar o Flutter, precisando ser instalado a parte, no entanto, é mantido pela mesma equipe.

## Informações sobre o package

#### Suporte

Plataformas (ou ambientes) nas quais o pacote é compatível:

- Android: SDK 16+

- iOS: 11.0+

- Linux: qualquer

- macOS: 10.14+

- Web: qualquer

- Windows: 10+

#### Instalação

Existe duas formas de instalar um package no Flutter (irei usar como exemplo de package o que será utilizado nesse tópico), que são as seguintes:

- Escrever no terminal e apertar `enter`:

```bash
flutter pub add url_launcher
```

ou

- Copiar o nome do pacote com sua versão e insirir diretamente nas dependencies, dessa forma:

```yaml
/// pubspec.yaml

dependencies:
  url_launcher: ^6.1.14
```

Depois colocar `flutter pub get` no terminal e enviar para obter o pacote.

#### Utilização

Existe várias maneiras de usar esse package, como, por exemplo, abrir em uma WebView, mas deixando isso de lado, vamos ao exemplo de caso de uso mais comum:

```dart
import 'package:flutter/material.dart';
import 'package:url_launcher/url_launcher.dart';

final Uri _url = Uri.parse('https://flutter.dev');

class MyApp extends StatelessWidget {
  const MyApp({super.key});

    Future<void> _launchUrl() async {
    if (!await launchUrl(_url)) {
      throw Exception('Não foi possível abrir $url');
    }
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
      body: Center(
        child: ElevatedButton(
          onPressed: () => _launchUrl(),
            child: const Text('Abrir'),
          ),
        ),
      ),
    );
  }
}
```

O funcionamento é bem simples, ao abertar no `ElevatedButton` ele chamará a função `_launchUrl`, que abrirá a URL, mas, caso dê algum erro, será lançado uma exceção.

</br>

## Referencências

<b>- [Documentação Oficial](https://pub.dev/packages/url_launcher)</b>
