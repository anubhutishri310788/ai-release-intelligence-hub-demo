# MCP Setup Guide - Secure Credential Configuration

## ⚠️ Security Fix Applied

Your Atlassian API token was exposed in `settings.json`. This has been **removed and replaced** with secure environment variable handling.

## 🔒 What Changed

**BEFORE (Insecure):**
```json
// ❌ Credentials hard-coded in settings.json
"Bash(export ATLASSIAN_HOST=\"anubhutishri123.atlassian.net\")",
"Bash(export ATLASSIAN_API_TOKEN=\"ATATT3xFfGF0...\")"
```

**AFTER (Secure):**
```bash
# ✅ Credentials in environment variables only
export ATLASSIAN_HOST="your-workspace.atlassian.net"
export ATLASSIAN_API_TOKEN="your-token-here"
```

## ⚡ IMMEDIATE ACTION REQUIRED

Since your API token was exposed in git history, you must **regenerate it immediately**:

1. Go to: https://id.atlassian.com/manage-profile/security/api-tokens
2. Click "Create API token"
3. Name it: `release-intelligence-hub-mcp`
4. Copy the new token
5. Use it in the setup instructions below

**Do NOT use the old token.** It's compromised.

---

## 📋 Setup Instructions (Choose One)

### Option 1: Using `.env` File (Recommended for Local Development)

1. **Create `.env` file in project root:**
   ```bash
   cp .env.example .env
   ```

2. **Edit `.env` with your credentials:**
   ```bash
   GITHUB_PERSONAL_ACCESS_TOKEN=ghp_your_github_token_here
   ATLASSIAN_HOST=your-workspace.atlassian.net
   ATLASSIAN_EMAIL=your-email@example.com
   ATLASSIAN_API_TOKEN=ATATT_your_new_atlassian_token_here
   ```

3. **Load environment variables (PowerShell):**
   ```powershell
   # Load from .env file
   Get-Content .env | foreach {
       $name, $value = $_.split('=')
       if ($name -and !$_.StartsWith('#')) {
           Set-Item -Path env:$name -Value $value
       }
   }
   
   # Verify they're loaded
   $env:ATLASSIAN_HOST
   $env:ATLASSIAN_API_TOKEN
   ```

4. **Load environment variables (Bash/WSL):**
   ```bash
   source .env
   echo $ATLASSIAN_HOST
   ```

### Option 2: Using Windows Environment Variables (System-Wide)

1. **Open System Environment Variables:**
   - Press `Win + X` → Select "System"
   - Click "Advanced system settings"
   - Click "Environment Variables"

2. **Add new variables under "User variables":**
   | Variable | Value |
   |----------|-------|
   | `GITHUB_PERSONAL_ACCESS_TOKEN` | `ghp_your_token_here` |
   | `ATLASSIAN_HOST` | `your-workspace.atlassian.net` |
   | `ATLASSIAN_EMAIL` | `your-email@example.com` |
   | `ATLASSIAN_API_TOKEN` | `ATATT_your_token_here` |

3. **Restart your IDE** for changes to take effect

### Option 3: Using PowerShell Profile (Permanent, Per-Session)

1. **Open your PowerShell profile:**
   ```powershell
   notepad $PROFILE
   ```

2. **Add this at the end:**
   ```powershell
   # MCP Environment Variables
   $env:GITHUB_PERSONAL_ACCESS_TOKEN = "ghp_your_token_here"
   $env:ATLASSIAN_HOST = "your-workspace.atlassian.net"
   $env:ATLASSIAN_EMAIL = "your-email@example.com"
   $env:ATLASSIAN_API_TOKEN = "ATATT_your_token_here"
   ```

3. **Save and reload:**
   ```powershell
   & $PROFILE
   ```

---

## 🔑 Getting Your Credentials

### GitHub Personal Access Token
1. Go to: https://github.com/settings/tokens
2. Click "Generate new token (classic)"
3. Set scopes: `repo` (full control of private repositories)
4. Copy the token

### Atlassian API Token
1. Go to: https://id.atlassian.com/manage-profile/security/api-tokens
2. Click "Create API token"
3. Copy the token (you won't see it again!)

### Atlassian Host & Email
- **Host:** `your-workspace.atlassian.net` (e.g., `anubhutishri123.atlassian.net`)
- **Email:** The email associated with your Atlassian account

---

## ✅ Verification

Once configured, verify your credentials are working:

**PowerShell:**
```powershell
$env:ATLASSIAN_HOST
$env:ATLASSIAN_EMAIL
$env:ATLASSIAN_API_TOKEN
$env:GITHUB_PERSONAL_ACCESS_TOKEN
```

All four should show values (tokens will be long strings).

---

## 🎯 Using Release Documentation Skill with Live MCP Data

Once credentials are set, run:

```bash
/release-documentation v2.5
```

Claude will:
1. Connect to GitHub MCP → Pull commits, contributors, PRs
2. Connect to Atlassian MCP → Pull Jira issues, Confluence pages
3. Generate all 4 documents with LIVE data
4. Audit and recommend

---

## 🛡️ Security Best Practices

| ✅ DO | ❌ DON'T |
|------|---------|
| Store credentials in `.env` | Hard-code in `settings.json` |
| Use `.env.example` as template | Commit `.env` to git |
| Regenerate if exposed | Reuse compromised tokens |
| Use strong, long tokens | Share tokens via email/chat |
| Rotate credentials monthly | Log credentials in console |
| Use git-ignored files | Leave tokens in code comments |

---

## 📝 Git Cleanup (Optional)

The exposed token is in your git history. To be extra safe, you can clean it:

```bash
# View the exposed token in history
git log --oneline | grep -i "atlassian\|token"

# (Advanced) Use git filter-branch or BFG to remove from history
# See: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository
```

---

## ❓ Troubleshooting

**Q: MCP still not connecting?**
- Verify environment variables are loaded: `$env:ATLASSIAN_HOST`
- Restart your IDE
- Check that tokens haven't expired

**Q: Getting "401 Unauthorized" from Atlassian?**
- Token may be expired or invalid
- Check you're using the NEW token (not the old exposed one)
- Verify email matches Atlassian account email

**Q: Can I use sample data instead?**
- Yes! The skill falls back to sample data if MCP isn't configured
- Run `/release-documentation v2.5` without setting env vars
- Skill will use built-in v2.5 template

---

## ✨ Done!

Your credentials are now:
- ✅ Removed from `settings.json`
- ✅ Safely stored in environment variables
- ✅ Git-ignored (won't be committed)
- ✅ Ready for MCP to use

You're ready to run the Release Documentation Skill with live data!
