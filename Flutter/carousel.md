# Carrossel no Flutter

Talvez já tenha feito essa pergunta alguma vez, ou ainda irá se deparar com uma situação que precisará criar um carrossel de imagens no Flutter. Embora exista formas de criar usando o PageView, é inegável que usar packages que lhe darão tudo o que irá precisar torna o processo mais fácil, prático e rápido.

Pensando nisso, irei apresentar o pacote `carousel_slider` que utilizo nos meus projetos para essa finalidade.

Se trata de uma biblioteca que simplifica a criação de carrosséis (ou sliders) de imagens ou widgets em seus aplicativos, seja ele Android, iOS, Web ou Desktop.

Carrosséis são componentes de interface de usuário comuns que permitem ao usuário navegar por uma lista de itens horizontalmente (ou verticalmente), geralmente exibindo um único item de cada vez ou múltiplos, de diferentes formas dependendo de como configurar.

## Informações sobre o package

#### Suporte

Plataformas (ou ambientes) nas quais o pacote é compatível:

- Android

- iOS

- Linux

- macOS

- Web

- Windows

#### Instalação

Existe duas formas de instalar um package no Flutter (irei usar como exemplo de package o que será utilizado nesse tópico), que são as seguintes:

- Escrever no terminal e apertar `enter`:

```bash
flutter pub add carousel_slider
```

ou

- Copiar o nome do pacote com sua versão e insirir diretamente nas dependencies, dessa forma:

```yaml
/// pubspec.yaml

dependencies:
    carousel_slider: ^4.2.1
```

Depois colocar `flutter pub get` no terminal e enviar para obter o pacote.

#### Utilização

Agora, vamos para um exemplo, embora básico, lhe dará uma noção de como funciona:

```dart
CarouselSlider(
 items: [
  Image.asset('assets/images/image1.jpg'),
  Image.asset('assets/images/image2.jpg'),
  Image.asset('assets/images/image3.jpg'),
 ],
 options: CarouselOptions(
  height: 300.0,
  aspectRatio: 16/9,
  enableInfiniteScroll: true,
 ),
)
```

Nesse exemplo, o carrossel contém três imagens, irá ter 300 de altura, proporção de 16/9 e rolagem infinita, o que significa que ao chegar na última imagem e deslizar para a direita, será mostrada a primeira imagem novamente.

</br>

## Referencências

<b>- [Documentação Oficial](https://pub.dev/packages/carousel_slider)</b>
