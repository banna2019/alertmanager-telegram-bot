# DEPRECATED

**This project is deprecated!** Please look at [alertmanager-notifier](https://gitlab.com/ix.ai/alertmanager-notifier), which is a drop-in replacement. Hence, this project is archived.

# alertmanager-telegram-bot

[![Pipeline Status](https://gitlab.com/ix.ai/alertmanager-telegram-bot/badges/master/pipeline.svg)](https://gitlab.com/ix.ai/alertmanager-telegram-bot/)
[![Docker Stars](https://img.shields.io/docker/stars/ixdotai/alertmanager-telegram-bot.svg)](https://hub.docker.com/r/ixdotai/alertmanager-telegram-bot/)
[![Docker Pulls](https://img.shields.io/docker/pulls/ixdotai/alertmanager-telegram-bot.svg)](https://hub.docker.com/r/ixdotai/alertmanager-telegram-bot/)
[![Gitlab Project](https://img.shields.io/badge/GitLab-Project-554488.svg)](https://gitlab.com/ix.ai/alertmanager-telegram-bot/)

A simple webserver in Flask, that translates [alertmanager](https://github.com/prometheus/alertmanager) alerts into telegram messages.

## Running a simple test:
```sh
docker run --rm -it \
    -p 9999:9999 \
    -e TELEGRAM_TOKEN="your token" \
    -e TELEGRAM_CHAT_ID="your chat id" \
    -e PORT=9999 \
    -e GELF_HOST=graylog \
    --name alertmanager-telegram-bot \
    registry.gitlab.com/ix.ai/alertmanager-telegram-bot:v0.6.0
```
Alterantively, use the hub.docker.com image:
```sh
docker run --rm -it \
    -p 9999:9999 \
    -e TELEGRAM_TOKEN="your token" \
    -e TELEGRAM_CHAT_ID="your chat id" \
    -e PORT=9999 \
    -e GELF_HOST=graylog \
    --name alertmanager-telegram-bot \
    ixdotai/alertmanager-telegram-bot:latest
```

Run the test agains the bot:
```sh
curl -X POST -d '{"alerts": [{"status":"Testing alertmanager-telegram-bot", "labels":[], "annotations":[], "generatorURL": "http://localhost"}]}' -H "Content-Type: application/json" localhost:9119/alert
```

## Configure alertmanager:
```yml
route:
  receiver: 'telegram alerts'
  routes:
    - receiver: 'telegram-webhook'

receivers:
  - name: 'telegram-webhook'
    webhook_configs:
      - url: http://alertmanager-telegram-bot:9119/alert
```

## Supported environment variables:

| **Variable**       | **Default**      | **Mandatory** | **Description**                                                                                                            |
|:-------------------|:----------------:|:-------------:|:---------------------------------------------------------------------------------------------------------------------------|
| `TELEGRAM_TOKEN`   | -                | **YES**       | see the [Telegram documentation](https://core.telegram.org/bots#creating-a-new-bot) how to get a new token                 |
| `TELEGRAM_CHAT_ID` | -                | **YES**       | see this question on [stackoverflow](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id) |
| `LOGLEVEL`         | `INFO`           | **NO**        | [Logging Level](https://docs.python.org/3/library/logging.html#levels)                                                     |
| `GELF_HOST`        | -                | **NO**        | if set, the exporter will also log to this [GELF](https://docs.graylog.org/en/3.0/pages/gelf.html) capable host on UDP     |
| `GELF_PORT`        | `12201`          | **NO**        | Ignored, if `GELF_HOST` is unset. The UDP port for GELF logging                                                            |
| `PORT`             | `9119`           | **NO**        | The port for prometheus metrics                                                                                            |
| `ADDRESS`          | `*`              | **NO**        | The address for the bot to listen on                                                                                       |
| `TEMPLATE`         | `notify.html.j2` | **NO**        | The template file to be used when creating the telegram message                                                            |

## Templating

**alertmanager-telegram-bot** supports jinja templating. take a look at [templates/notify.html.j2](templates/notify.html.j2) for that. If you want to use your own template, mount it as a volume in docker and set the `TEMPLATE` environment variable. The mount path should be under `/templates/` (for example `/templates/my-amazing-template`).

## Tags and Arch

Starting with version v0.6.1, the images are multi-arch, with builds for amd64, arm64, armv7 and armv6.
* `vN.N.N` - for example v0.6.0
* `latest` - always pointing to the latest version
* `dev-branch` - the last build on a feature/development branch
* `dev-master` - the last build on the master branch

## Resources:
* GitLab: https://gitlab.com/ix.ai/alertmanager-telegram-bot
* GitHub: https://github.com/ix-ai/alertmanager-telegram-bot
* Docker Hub: https://hub.docker.com/r/ixdotai/alertmanager-telegram-bot

## Credits:
This work is inspired by [nopp/alertmanager-webhook-telegram](https://github.com/nopp/alertmanager-webhook-telegram)
