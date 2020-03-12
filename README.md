# Instalação OpenVPN

Instalador OpenVPN para Debian, Ubuntu, Fedora, CentOS e Arch Linux.

Esse script permitirá que você configure seu próprio servidor VPN seguro em apenas alguns segundos.

Você também pode conferir [wireguard-install](https://github.com/angristan/wireguard-install), a simple installer for a simpler, safer, faster and more modern VPN protocol.

## Uso

Primeiro, obtenha o script e o torne executável:

```bash
curl -O https://raw.githubusercontent.com/DanielMendes/openvpn-install/master/openvpn-install.sh
chmod +x openvpn-install.sh
```

Em seguida, execute-o:

```sh
./openvpn-install.sh
```

Você precisa executar o script como root e ter o módulo TUN ativado.

Na primeira vez em que você o executar, será necessário seguir o assistente e responder algumas perguntas para configurar seu servidor VPN.

Quando o OpenVPN estiver instalado, você poderá executar o script novamente e poderá escolher:

- Adicionar um cliente
- Remover um cliente
- Desinstalar OpenVPN

No seu diretório pessoal, você terá arquivos `.ovpn`. Esses são os arquivos de configuração do cliente. Faça o download do seu servidor e conecte-se usando seu cliente OpenVPN favorito.

Se você tiver alguma dúvida, vá primeiro ao [FAQ] (# faq).

### Instalação automatizada

Também é possível executar o script sem interação do usuário, por exemplo: sem esperar pela entrada do usuário. De maneira automatizada.

Exemplo de uso:
```bash
AUTO_INSTALL=y ./openvpn-install.sh

# or

export AUTO_INSTALL=y
./openvpn-install.sh
```

Um conjunto padrão de variáveis ​​será então definido, passando a necessidade de entrada do usuário.

Se você deseja personalizar sua instalação, pode exportá-los ou especificá-los na mesma linha, como mostrado acima.

- `APPROVE_INSTALL=y`
- `APPROVE_IP=y`
- `IPV6_SUPPORT=n`
- `PORT_CHOICE=1`
- `PROTOCOL_CHOICE=1`
- `DNS=1`
- `COMPRESSION_ENABLED=n`
- `CUSTOMIZE_ENC=n`
- `CLIENT=clientname`
- `PASS=1`

Se o servidor estiver atrás do NAT, você poderá especificar seu ponto final com a variável `ENDPOINT`. Se o ponto final for o endereço IP público atrás do qual está localizado, você poderá usar `ENDPOINT = $ (curl -4 ifconfig.co)` (o script será o padrão para isso). O terminal pode ser um IPv4 ou um domínio.

Outras variáveis podem ser definidas dependendo da sua escolha (criptografia, compactação). Você pode procurá-los na função `installQuestions ()` do script.

Os clientes protegidos por senha não são suportados pelo método de instalação sem cabeçalho, pois a entrada do usuário é esperada pelo Easy-RSA.

### Adicionar usuário automaticamente

Também é possível automatizar a adição de um novo usuário. Aqui, a chave é fornecer o valor (string) da variável `MENU_OPTION` junto com as variáveis obrigatórias restantes antes de chamar o script.

O script Bash a seguir adiciona um novo usuário `foo` a uma configuração OpenVPN existente
```bash
#!/bin/bash
export MENU_OPTION="1"
export CLIENT="foo"
export PASS="1"
./openvpn-install.sh
```

## Recursos

- Instala e configura um servidor OpenVPN pronto para uso 
- Regras e encaminhamento de iptables gerenciados de maneira integrada
- Se necessário, o script pode remover claramente o OpenVPN, incluindo regras de configuração e iptables
- Configurações de criptografia personalizáveis, configurações padrão aprimoradas (consulte [Segurança e criptografia] (#security-and-encryption) abaixo)
- Recursos do OpenVPN 2.4, principalmente melhorias na criptografia (consulte [Segurança e criptografia] (#security-and-encryption) abaixo)
- Variedade de resolvedores de DNS a serem enviados aos clientes
- Escolha usar um resolvedor auto-hospedado com o Unbound (suporta instalações já existentes do Unbound)
- Escolha entre TCP e UDP
- Suporte IPv6 com NAT
- Compactação desativada por padrão para impedir o VORACLE. Caso contrário, os algoritmos LZ4 (v1 / v2) e LZ0 estão disponíveis.
- Modo não privilegiado: execute como `nobody` /` nogroup`
- Bloqueie vazamentos de DNS no Windows 10
- Nome aleatório do certificado do servidor
- Escolha para proteger os clientes com uma senha (criptografia de chave privada)
- Muitas outras coisinhas!

## Compatibilidade

O script suporta estes sistemas operacionais e arquiteturas:

|                | i386 | amd64 | armhf | arm64 |
| -------------- | ---- | ----- | ----- | ----- |
| Amazon Linux 2 |  ❔  |  ✅  |   ❔  |   ❔  |
|  Arch Linux    |  ❔  |  ✅  |   ❔  |   ✅  |
|   Centos 8     |  ❌  |  ✅  |   ❔  |   ❔  |
|   CentOS 7     |  ❔  |  ✅  |   ❌  |   ✅  |
|   Debian 8     |  ✅  |  ✅  |   ❌  |   ❌  |
|   Debian 9     |  ❌  |  ✅  |   ✅  |   ✅  |
|   Debian 10    |  ❔  |  ✅  |   ✅  |   ❔  |
|   Fedora 27    |  ❔  |  ✅  |   ❔  |   ❔  |
|   Fedora 28    |  ❔  |  ✅  |   ❔  |   ❔  |
| Ubuntu 16.04   |  ✅  |  ✅  |   ❌  |   ❌  |
| Ubuntu 18.04   |  ❌  |  ✅  |   ✅  |   ✅  |
| Ubuntu 19.04   |  ❌  |  ✅  |   ✅  |   ✅  |

A notar:

- O script deve funcionar no Debian 8+ e no Ubuntu 16.04+. Mas versões que não estão na tabela acima não são oficialmente suportadas.
- O script requer `systemd`.
- O script é testado regularmente somente na arquitetura `amd64`.

## Fork

This script is based on the great work of [Nyr and its contributors](https://github.com/Nyr/openvpn-install).

Since 2016, the two scripts have diverged and are not alike anymore, especially under the hood. The main goal of the script was enhanced security. But since then, the script has been completely rewritten and a lot a features have been added. The script is only compatible with recent distributions though, so if you need to use a very old server or client, I advise using Nyr's script.

## FAQ

**LOOK AT THE [WIKI](https://github.com/angristan/openvpn-install/wiki/FAQ) FOR MORE INFORMATION. PLEASE READ BOTH BEFORE OPENING AN ISSUE.**

**PLEASE do net send me emails or private messages asking for help.** The only place to get help is the issues. Other people may be able to help and in the future, other users may also run into the same issue as you.

**Q:** Which provider do you recommend?

**A:** I recommend these:

- [Vultr](https://goo.gl/Xyd1Sc): Worldwide locations, IPv6 support, starting at $3.50/month
- [PulseHeberg](https://goo.gl/76yqW5): France, unlimited bandwidth, starting at €3/month
- [Digital Ocean](https://goo.gl/qXrNLK): Worldwide locations, IPv6 support, starting at $5/month

---

**Q:** Which OpenVPN client do you recommend?

**A:** If possible, an official OpenVPN 2.4 client.

- Windows: [The official OpenVPN community client](https://openvpn.net/index.php/download/community-downloads.html).
- Linux: The `openvpn` package from your distribution. There is an [official APT repository](https://community.openvpn.net/openvpn/wiki/OpenvpnSoftwareRepos) for Debian/Ubuntu based distributions.
- macOS: [Tunnelblick](https://tunnelblick.net/), [Viscosity](https://www.sparklabs.com/viscosity/).
- Android: [OpenVPN for Android](https://play.google.com/store/apps/details?id=de.blinkt.openvpn).
- iOS: [The official OpenVPN Connect client](https://itunes.apple.com/us/app/openvpn-connect/id590379981).

---

**Q:** Am I safe from the NSA by using your script?

**A:** Please review your threat models. Even if this script has security in mind and uses state-of-the-art encryption, you shouldn't be using a VPN if you want to hide from the NSA.

---

**Q:** Is there an OpenVPN documentation?

**A:** Yes, please head to the [OpenVPN Manual](https://community.openvpn.net/openvpn/wiki/Openvpn24ManPage), which references all the options.

---

##  One-stop solutions for public cloud

Solutions that provision a ready to use OpenVPN server based on this script in one go are available for:

 - AWS using Terraform at [`openvpn-terraform-install`](https://github.com/dumrauf/openvpn-terraform-install)


## Security and Encryption

OpenVPN's default settings are pretty weak regarding encryption. This script aims to improve that.

OpenVPN 2.4 was a great update regarding encryption. It added support for ECDSA, ECDH, AES GCM, NCP and tls-crypt.

If you want more information about an option mentioned below, head to the [OpenVPN manual](https://community.openvpn.net/openvpn/wiki/Openvpn24ManPage). It is very complete.

Most of OpenVPN's encryption-related stuff is managed by [Easy-RSA](https://github.com/OpenVPN/easy-rsa). Defaults parameters are in the [vars.example](https://github.com/OpenVPN/easy-rsa/blob/v3.0.6/easyrsa3/vars.example) file.
### Compression

By default, OpenVPN doesn't enable compression. This script provides support for LZ0 and LZ4 (v1/v2) algorithms, the latter being more efficient.

However, it is discouraged to use compression since it since the [VORACLE attack](https://protonvpn.com/blog/voracle-attack/) makes use of it.

### TLS version

OpenVPN accepts TLS 1.0 by default, which is nearly [20 years old](https://en.wikipedia.org/wiki/Transport_Layer_Security#TLS_1.0).

With `tls-version-min 1.2` we enforce TLS 1.2, which the best protocol available currently for OpenVPN.

TLS 1.2 is supported since OpenVPN 2.3.3.

### Certificate

OpenVPN uses an RSA certificate with a 2048 bits key by default.

OpenVPN 2.4 added support for ECDSA. Elliptic curve cryptography is faster, lighter and more secure.

This script provides:

- ECDSA: `prime256v1`/`secp384r1`/`secp521r1` curves
- RSA: `2048`/`3072`/`4096` bits keys

It defaults to ECDSA with `prime256v1`.

OpenVPN uses `SHA-256` as the signature hash by default, and so does the script. It provides no other choice as of now.

### Data channel

By default, OpenVPN uses `BF-CBC` as the data channel cipher. Blowfish is an old (1993) and weak algorithm. Even the official OpenVPN documentation admits it.

> The default is BF-CBC, an abbreviation for Blowfish in Cipher Block Chaining mode.
>
> Using BF-CBC is no longer recommended, because of its 64-bit block size. This small block size allows attacks based on collisions, as demonstrated by SWEET32. See https://community.openvpn.net/openvpn/wiki/SWEET32 for details.

>Security researchers at INRIA published an attack on 64-bit block ciphers, such as 3DES and Blowfish. They show that they are able to recover plaintext when the same data is sent often enough, and show how they can use cross-site scripting vulnerabilities to send data of interest often enough. This works over HTTPS, but also works for HTTP-over-OpenVPN. See ​https://sweet32.info/ for a much better and more elaborate explanation.
>
> OpenVPN's default cipher, BF-CBC, is affected by this attack.

Indeed, AES is today's standard. It's the fastest and more secure cipher available today. [SEED](https://en.wikipedia.org/wiki/SEED) and [Camellia](https://en.wikipedia.org/wiki/Camellia_(cipher)) are not vulnerable to date but are slower than AES and relatively less trusted.

> Of the currently supported ciphers, OpenVPN currently recommends using AES-256-CBC or AES-128-CBC. OpenVPN 2.4 and newer will also support GCM. For 2.4+, we recommend using AES-256-GCM or AES-128-GCM.

AES-256 is 40% slower than AES-128, and there isn't any real reason to use a 256 bits key over a 128 bits key with AES. (Source : [1](http://security.stackexchange.com/questions/14068/why-most-people-use-256-bit-encryption-instead-of-128-bit),[2](http://security.stackexchange.com/questions/6141/amount-of-simple-operations-that-is-safely-out-of-reach-for-all-humanity/6149#6149)). Moreover, AES-256 is more vulnerable to [Timing attacks](https://en.wikipedia.org/wiki/Timing_attack).

AES-GCM is an [AEAD cipher](https://en.wikipedia.org/wiki/Authenticated_encryption) which means it simultaneously provides confidentiality, integrity, and authenticity assurances on the data.

The script supports the following ciphers:

- `AES-128-GCM`
- `AES-192-GCM`
- `AES-256-GCM`
- `AES-128-CBC`
- `AES-192-CBC`
- `AES-256-CBC`

And defaults to `AES-128-GCM`.

OpenVPN 2.4 added a feature called "NCP": *Negotiable Crypto Parameters*. It means you can provide a cipher suite like with HTTPS. It is set to `AES-256-GCM:AES-128-GCM` by default and overrides the `--cipher` parameter when used with an OpenVPN 2.4 client. For the sake of simplicity, the script set both the `--cipher` and `--ncp-cipher` to the cipher chosen above.

### Control channel

OpenVPN 2.4 will negotiate the best cipher available by default (e.g ECDHE+AES-256-GCM)

The script proposes the following options, depending on the certificate:

- ECDSA:
  - `TLS-ECDHE-ECDSA-WITH-AES-128-GCM-SHA256`
  - `TLS-ECDHE-ECDSA-WITH-AES-256-GCM-SHA384`
- RSA:
  - `TLS-ECDHE-RSA-WITH-AES-128-GCM-SHA256`
  - `TLS-ECDHE-RSA-WITH-AES-256-GCM-SHA384`

It defaults to `TLS-ECDHE-*-WITH-AES-128-GCM-SHA256`.

### Diffie-Hellman key exchange

OpenVPN uses a 2048 bits DH key by default.

OpenVPN 2.4 added support for ECDH keys. Elliptic curve cryptography is faster, lighter and more secure.

Also, generating a classic DH keys can take a long, looong time. ECDH keys are ephemeral: they are generated on-the-fly.

The script provides the following options:

- ECDH: `prime256v1`/`secp384r1`/`secp521r1` curves
- DH: `2048`/`3072`/`4096` bits keys

It defaults to `prime256v1`.

### HMAC digest algorithm

From the OpenVPN wiki, about `--auth`:

> Authenticate data channel packets and (if enabled) tls-auth control channel packets with HMAC using message digest algorithm alg. (The default is SHA1 ). HMAC is a commonly used message authentication algorithm (MAC) that uses a data string, a secure hash algorithm, and a key, to produce a digital signature.
>
> If an AEAD cipher mode (e.g. GCM) is chosen, the specified --auth algorithm is ignored for the data channel, and the authentication method of the AEAD cipher is used instead. Note that alg still specifies the digest used for tls-auth.

The script provides the following choices:

- `SHA256`
- `SHA384`
- `SHA512`

It defaults to `SHA256`.

### `tls-auth` and `tls-crypt`

From the OpenVPN wiki, about `tls-auth`:

> Add an additional layer of HMAC authentication on top of the TLS control channel to mitigate DoS attacks and attacks on the TLS stack.
>
> In a nutshell, --tls-auth enables a kind of "HMAC firewall" on OpenVPN's TCP/UDP port, where TLS control channel packets bearing an incorrect HMAC signature can be dropped immediately without response.

About `tls-crypt`:

> Encrypt and authenticate all control channel packets with the key from keyfile. (See --tls-auth for more background.)
>
> Encrypting (and authenticating) control channel packets:
> - provides more privacy by hiding the certificate used for the TLS connection,
> - makes it harder to identify OpenVPN traffic as such,
> - provides "poor-man's" post-quantum security, against attackers who will never know the pre-shared key (i.e. no forward secrecy).

So both provide an additional layer of security and mitigate DoS attacks. They aren't used by default by OpenVPN.

`tls-crypt` is an OpenVPN 2.4 feature that provides encryption in addition to authentication (unlike `tls-auth`). It is more privacy-friendly.

The script supports both and uses `tls-crypt` by default.

## Say thanks

*Sadly saythanks.io doesn't exist anymore... Thanks for the dozens of messages! It's really meaninful to me.*

*Still want to help? Check the "sponsor" button at the top of the page!*

## Credits & Licence

Many thanks to the [contributors](https://github.com/Angristan/OpenVPN-install/graphs/contributors) and Nyr's original work.

This project is under the [MIT Licence](https://raw.githubusercontent.com/Angristan/openvpn-install/master/LICENSE)
