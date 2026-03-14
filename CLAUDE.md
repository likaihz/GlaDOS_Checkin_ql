# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GLaDOS自动签到 - A Python script for automatic daily check-in on [GLaDOS](https://glados.rocks/) (a V2Ray/Trojan VPN service). Daily check-ins extend membership by one day.

## Commands

### Run the checkin script
```bash
python checkin.py
```

### Install dependencies
```bash
# China (Tsinghua mirror)
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# International
pip install -r requirements.txt
```

## Architecture

```
checkin.py (Entry Point)
    ├── get_cookies()     → Read cookies from env var GR_COOKIE or config.py
    ├── checkin()         → HTTP POST to GLaDOS API
    └── load_send()       → Load notification module

sendNotify.py (Notification Hub)
    └── send()            → Multi-threaded dispatch to configured channels
        ├── bark()        → iOS Bark app
        ├── telegram_bot()
        ├── wecom_app()   → WeChat Work app
        ├── wecom_bot()   → WeChat Work bot
        ├── dingding_bot()
        ├── feishu_bot()
        ├── pushplus_bot()
        └── ... (15+ channels total)

config.py (Local Configuration)
    └── Cookies = [...]   → Fallback when GR_COOKIE env var not set
```

## Configuration

### Cookie Format
```
koa:sess=xxxxxxxxx; koa:sess.sig=xxxx;
```

### Multi-Account Setup

**Qinglong Panel (recommended):**
- Set `GR_COOKIE` environment variable, multiple accounts separated by `&` or newline
- Pull repo: `ql repo https://github.com/hennessey-v/GlaDOS_Checkin_ql.git "checkin.py" "backUp|assets|README.md" "sendNotify.py"`

**Local deployment:**
- Edit `config.py`, add cookies to the `Cookies` list

### Notification Configuration

All notification channels are configured via environment variables in `sendNotify.py`:
- `BARK_PUSH` - iOS Bark
- `TG_BOT_TOKEN` + `TG_USER_ID` - Telegram
- `QYWX_AM` - WeChat Work app
- `QYWX_KEY` - WeChat Work bot
- `DD_BOT_TOKEN` + `DD_BOT_SECRET` - DingDing
- `FSKEY` - Feishu
- `PUSH_PLUS_TOKEN` - PushPlus
- See `sendNotify.py` for full list

## Key APIs

- Checkin: `POST https://glados.rocks/api/user/checkin` with `{"token": "glados.one"}`
- Status: `GET https://glados.rocks/api/user/status`