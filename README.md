# cloudflared-tunnel skill

Agent skill để tạo **quick tunnel** bằng `cloudflared tunnel --url`, expose một
local web server/port ra internet qua Cloudflare — không cần tài khoản, không cần DNS.

Skill được viết theo [Agent Skills](https://agentskills.io) (mở, đa agent) — hoạt động
với OpenCode, Claude Code, Codex, Cursor và nhiều agent khác.

## Cấu trúc

```
skills/
└── cloudflared-tunnel/
    └── SKILL.md
```

## Cài đặt

### Cách 1: `skills` CLI (khuyên dùng — cài được qua internet)

```bash
# Thử/hiển thị
npx skills add namwasinyourheart/cloudflared-tunnel-skill --list

# Cài skill
npx skills add namwasinyourheart/cloudflared-tunnel-skill --skill cloudflared-tunnel
```

Thêm `-g` để cài toàn cục, `-a opencode`/`-a claude-code` để chọn agent đích:
```bash
npx skills add namwasinyourheart/cloudflared-tunnel-skill --skill cloudflared-tunnel -a opencode -g
```

### Cách 2: OpenCode — clone + trỏ `skills.paths`

```bash
git clone https://github.com/namwasinyourheart/cloudflared-tunnel-skill.git
```

Rồi trong `opencode.json`:
```json
{
  "$schema": "https://opencode.ai/config.json",
  "skills": { "paths": ["/đường/dẫn/đến/cloudflared-tunnel-skill/skills"] }
}
```

Sau khi cấu hình, **thoát và khởi động lại opencode** để skill được nạp.

### Cách 3: copy thủ công (tương thích chéo)

Copy thư mục skill vào đúng path của agent:
```bash
mkdir -p ~/code/cloudflared-tunnel-skill/skills
cp -r skills/cloudflared-tunnel ~/.config/opencode/skills/          # opencode (global)
cp -r skills/cloudflared-tunnel ~/.claude/skills/                   # claude-code (global)
cp -r skills/cloudflared-tunnel .opencode/skills/                   # opencode (project)
```

## Nội dung skill

SKILL.md hướng dẫn:
- Cài `cloudflared` (macOS/Linux/Windows)
- Chạy `cloudflared tunnel --url http://localhost:PORT` và lấy URL `*.trycloudflare.com`
- Các option hay dùng (`--logfile`, `--no-autoupdate`, `--loglevel`)
- Lưu ý về tunnel tạm thời, lỗi 502, và bảo mật khi expose

## License

MIT
