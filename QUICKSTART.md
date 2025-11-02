# 🚀 n8n Docker Quick Start Guide

## ✅ Setup Complete!

Your n8n instance is now running via Docker!

---

## 📍 Access n8n

**URL**: http://localhost:5678

**Default Credentials**:
- Username: `admin`
- Password: `n8n_admin_2024`

> **Important**: Change these credentials in the `.env` file for production use!

---

## 🎯 Next Steps

### 1. Access n8n
Open your browser and navigate to: http://localhost:5678

### 2. Import Your First Workflow

**Via UI**:
1. Click **"Add workflow"** → **"Import from File"**
2. Browse to `/workflows` or `/automation` folder
3. Select any `.json` file
4. Click **"Import"**

**Recommended Starter Workflows**:
- `workflows/ai-agents/communication/` - AI chatbots
- `workflows/data-integration/1898-send-a-chatgpt-email-reply-and-save-responses-to-google-sheets/`
- `automation/` - 900+ ready-to-use automations

### 3. Configure API Credentials

Most workflows need API keys. To add credentials:

1. Click on any node with a red outline (missing credentials)
2. Click **"Create New Credential"**
3. Add your API keys:
   - **OpenAI**: https://platform.openai.com/api-keys
   - **Anthropic Claude**: https://console.anthropic.com/
   - **Google**: https://console.cloud.google.com/
   - **Others**: Check each service's documentation

---

## 🔧 Docker Management Commands

### View Container Status
```bash
docker ps
```

### View Logs
```bash
docker logs n8n
docker logs n8n -f  # Follow logs in real-time
```

### Stop n8n
```bash
docker compose stop
```

### Start n8n (after stopping)
```bash
docker compose start
```

### Restart n8n
```bash
docker compose restart
```

### Stop and Remove (keeps data)
```bash
docker compose down
```

### Stop and Remove Everything (deletes data!)
```bash
docker compose down -v  # ⚠️ This deletes all workflows and data!
```

---

## 📂 Data Persistence

Your n8n data is stored in a Docker volume and will persist even if you stop/restart containers:
- **Workflows**: Saved automatically
- **Credentials**: Encrypted and stored securely
- **Execution history**: Maintained in the database

**Backup Location**: Docker volume `ultimate-n8n-ai-workflows_n8n_data`

---

## ⚙️ Configuration

### Edit Environment Variables
```bash
# Edit the .env file
nano .env  # or use your preferred editor

# After making changes, restart:
docker compose restart
```

### Important Settings in `.env`:

| Variable | Purpose | Default |
|----------|---------|---------|
| `N8N_BASIC_AUTH_USER` | Login username | admin |
| `N8N_BASIC_AUTH_PASSWORD` | Login password | n8n_admin_2024 |
| `TIMEZONE` | Your timezone | America/New_York |
| `WEBHOOK_URL` | External webhook URL | http://localhost:5678/ |

---

## 🔐 Security Recommendations

1. **Change Default Password**:
   Edit `.env` and update `N8N_BASIC_AUTH_PASSWORD`

2. **For Production**:
   - Use PostgreSQL instead of SQLite (uncomment in `docker-compose.yml`)
   - Set up HTTPS with a reverse proxy (nginx/Caddy)
   - Use strong, unique passwords
   - Enable 2FA if available

3. **API Keys**:
   - Never commit `.env` to git
   - Store sensitive keys in n8n's credential system
   - Use environment variables for additional security

---

## 📚 Workflow Categories

This repository contains 3,400+ workflows organized by category:

### `/workflows/ai-agents/`
- **communication/** - Chatbots, messaging automations
- **data-integration/** - Connect apps and databases
- **document-processing/** - PDF, OCR, document parsing
- **data-transformation/** - ETL and data processing

### `/automation/`
- 900+ general purpose automations
- Scheduled tasks
- Data sync workflows
- Monitoring and alerts

### `/workflows/analytics/`
- Reports and dashboards
- Feedback analysis
- Statistics tracking

---

## 💡 Tips for Success

1. **Start Small**: Import a simple workflow to understand the basics
2. **Test Manually**: Use "Execute Workflow" button before scheduling
3. **Check Credentials**: Ensure all API keys are valid
4. **Read Node Docs**: Click the "?" icon on any node for documentation
5. **Use Templates**: Customize existing workflows rather than building from scratch

---

## 🆘 Troubleshooting

### n8n won't start
```bash
# Check logs
docker logs n8n

# Ensure port 5678 is free
lsof -i :5678

# Restart Docker
docker compose down && docker compose up -d
```

### Can't access http://localhost:5678
- Wait 10-15 seconds after starting (check with `docker logs n8n`)
- Verify container is running: `docker ps`
- Check firewall settings

### Workflows not importing
- Ensure JSON file is valid
- Check for missing node types (install missing integrations)
- Verify n8n version compatibility

### Credentials not saving
- Check file permissions on Docker volume
- Ensure container has write access
- Restart n8n: `docker compose restart`

---

## 📖 Additional Resources

- **n8n Documentation**: https://docs.n8n.io/
- **Community Forum**: https://community.n8n.io/
- **Workflow Templates**: `/docs/` folder in this repo
- **Prompt Engineering Tips**: `/docs/prompt-engineering-tips.md`

---

## 🎓 Learn More

### Import Tutorial
See `/docs/how-to-import.md` for detailed import instructions

### Architecture Overview
See `/docs/architecture.md` for system design details

### Contributing
Want to add your own workflows? See `CONTRIBUTING.md`

---

## 🚀 You're Ready!

Your n8n instance is running and ready to automate!

**Access it now**: http://localhost:5678

Happy automating! 🎉
