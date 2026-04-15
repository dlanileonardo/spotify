# Spotify Connect Speaker (Raspotify + Docker)

Transforma qualquer host Linux com saída de áudio em um dispositivo **Spotify Connect** usando `raspotify` dentro de container.

## Imagem pública (GHCR)

A imagem está publicada em:

`ghcr.io/dlanileonardo/spotify:main`

Também são publicadas as tags `latest` e `sha-*`.

Para usar referência imutável, utilize digest:

```bash
docker pull ghcr.io/dlanileonardo/spotify@sha256:<digest>
```

Pacote: https://github.com/dlanileonardo/spotify/pkgs/container/spotify

## Recursos

- Spotify Connect via `librespot` (`raspotify`)
- Backend ALSA com suporte a device customizado
- Equalizador opcional via `alsaequal`
- Volume inicial configurável por variável de ambiente
- Persistência de cache/lib em volumes locais

## Pré-requisitos

- Linux com Docker + Docker Compose
- Dispositivo de áudio acessível em `/dev/snd`
- Conta Spotify Premium (exigência do Spotify Connect)

## Uso rápido

### 1) Configurar variáveis de ambiente

```bash
cp .env.example .env
```

Edite o `.env` com o nome do dispositivo e saída de áudio desejada.

### 2) Baixar imagem pública

```bash
docker compose pull
```

### 3) Subir serviço

```bash
docker compose up -d
```

### 4) Ver logs

```bash
docker compose logs -f raspotify
```

## Build local (opcional)

Se você quiser testar alterações locais no `Dockerfile`, rode:

```bash
docker compose build --no-cache
docker compose up -d
```

## Variáveis de ambiente

- `SPOTIFY_NAME`: nome exibido no Spotify Connect
- `BACKEND_NAME`: backend de áudio (`alsa` por padrão)
- `DEVICE_NAME`: device ALSA de saída (ex.: `default`, `equal`, `bluealsa:...`)
- `ALSA_SLAVE_PCM`: device final usado no `asound.conf` (quando usar equalizador)
- `ALSA_SOUND_LEVEL`: volume inicial (ex.: `100%`)
- `VERBOSE`: `true` para logs verbosos do `librespot`
- `EQUALIZATION`: preset (`flat`, `rock`, `bass`, etc.) ou curva manual

## Estrutura

- `compose.yaml`: serviço e volumes
- `Dockerfile`: imagem com `raspotify` e utilitários ALSA
- `startup.sh`: geração de config + bootstrap do `librespot`
- `asound.conf`: template ALSA (usa `envsubst`)
- `equalizer.sh`: presets do equalizador
- `raspotify/cache` e `raspotify/lib`: persistência local

## Segurança e privacidade

- **Não publique** o arquivo `.env` com dados reais do seu ambiente.
- Endereços Bluetooth/MAC em `DEVICE_NAME` podem identificar seus dispositivos.
- Se um segredo já foi commitado no passado, troque-o e reescreva o histórico antes de republicar.

## Troubleshooting

- Sem áudio: valide permissões de `/dev/snd` e o `DEVICE_NAME`.
- Device não aparece no Spotify: confira logs e nome em `SPOTIFY_NAME`.
- BlueALSA: garanta que os sockets montados (`/var/run/dbus`, `/var/run/bluealsa`) existam no host.

## Licença

Defina aqui a licença do projeto (recomendado adicionar um arquivo `LICENSE`).
