# mia
Repositório de skills, plugins, etc.

Também é uma marketplace de plugins do Claude Code (`.claude-plugin/marketplace.json`). Plugins ficam em `plugins/<nome>/`.

## Plugins

- [`security-architecture`](plugins/security-architecture) — skills para revisar arquitetura/design sob a ótica de segurança. Primeira skill: OWASP Top 10 (2021).
- [`application-architecture`](plugins/application-architecture) — skills para projetar e revisar arquitetura de aplicações (hexagonal, clean, event-driven/CQRS), design patterns (GoF) e princípios de design (SOLID, KISS, Tell Don't Ask, etc.).

Testado em desenvolvimento a partir do repositório `testing-mia`.

## Releases e como consumir

- `main` é o estado liberado; `develop` é integração. Releases são tags imutáveis em `main` (`v0.2.0`, ...), registradas no [CHANGELOG](CHANGELOG.md).
- A tag versiona a marketplace inteira. Cada plugin tem sua própria versão em `plugins/<nome>/.claude-plugin/plugin.json` e evolui dentro dela.

Consumidores devem **fixar uma tag**, nunca apontar para um branch — apontar para `develop` faz cada sessão puxar o que estiver no branch, mudando o comportamento das skills sem revisão:

```json
{
  "extraKnownMarketplaces": {
    "mia": {
      "source": {
        "source": "git",
        "url": "https://github.com/rodx64/mia.git",
        "ref": "v0.2.0"
      }
    }
  },
  "enabledPlugins": {
    "application-architecture@mia": true
  }
}
```

Atualizar de versão é uma mudança deliberada: subir o `ref` para a tag nova, em um commit revisável no repositório consumidor.
