# Wrangler Quick Reference Guide

## What is Wrangler?

Wrangler is Cloudflare's CLI tool for managing Workers (serverless functions that run on Cloudflare's edge network). Think of it as your deployment and development tool for serverless JavaScript/TypeScript code.

---

## Installation

### Local Installation (Recommended)
```bash
npm install -D wrangler
```

**Why local?**
- Version locked per project
- Team uses same version
- Documented in package.json

### Global Installation
```bash
npm install -g wrangler
```

### Check Version
```bash
wrangler --version
```

---

## Core Commands

### Development

#### Start Local Dev Server
```bash
wrangler dev
# Opens at http://localhost:8787
# Hot reload enabled
# Uses local KV/Durable Objects
```

**Options:**
```bash
wrangler dev --port 3000           # Custom port
wrangler dev --local               # Completely local (no Cloudflare API)
wrangler dev --remote              # Run on Cloudflare edge
wrangler dev --test-scheduled      # Trigger cron handlers
```

---

### Deployment

#### Deploy to Production
```bash
wrangler deploy
```

**What happens:**
1. Bundles your code (esbuild)
2. Validates syntax
3. Uploads to Cloudflare
4. Deploys globally (~10 seconds)

#### Deploy to Specific Environment
```bash
wrangler deploy --env staging
wrangler deploy --env production
```

Requires `wrangler.toml` config:
```toml
[env.staging]
name = "my-worker-staging"

[env.production]
name = "my-worker-prod"
```

#### Dry Run (Test Build Without Deploying)
```bash
wrangler deploy --dry-run
# Validates code but doesn't upload
```

---

### Deployment Management

#### List Recent Deployments
```bash
wrangler deployments list
```

**Output:**
```
Deployment ID        | Created On           | Author
---------------------|----------------------|------------
abc123               | 2025-10-07 18:55:43  | you@email
def456               | 2025-10-07 17:30:22  | you@email
```

#### View Current Deployment
```bash
wrangler deployments status
```

#### Rollback to Previous Version
```bash
wrangler rollback

# Or specify deployment ID:
wrangler rollback --deployment-id abc123
```

---

### KV (Key-Value Storage)

#### List KV Namespaces
```bash
wrangler kv:namespace list
```

#### Create KV Namespace
```bash
wrangler kv:namespace create "SESSIONS"
# Returns: { "id": "abc123...", "title": "SESSIONS" }

# For preview (dev):
wrangler kv:namespace create "SESSIONS" --preview
```

Add to `wrangler.toml`:
```toml
kv_namespaces = [
  { binding = "SESSIONS", id = "abc123..." }
]
```

#### KV Operations

**Put key:**
```bash
wrangler kv:key put --namespace-id=abc123 "mykey" "myvalue"
```

**Get key:**
```bash
wrangler kv:key get --namespace-id=abc123 "mykey"
```

**List keys:**
```bash
wrangler kv:key list --namespace-id=abc123
wrangler kv:key list --namespace-id=abc123 --prefix="sess:"
```

**Delete key:**
```bash
wrangler kv:key delete --namespace-id=abc123 "mykey"
```

**Bulk upload:**
```bash
# Create keys.json: [{"key": "k1", "value": "v1"}, ...]
wrangler kv:bulk put --namespace-id=abc123 keys.json
```

---

### Secrets Management

#### Set Secret
```bash
wrangler secret put DCT2_SECRET
# Prompts for value (hidden input)
```

**Via file:**
```bash
echo "my-secret-value" | wrangler secret put DCT2_SECRET
```

#### List Secrets (Names Only)
```bash
wrangler secret list
# Shows names, NOT values (security)
```

#### Delete Secret
```bash
wrangler secret delete DCT2_SECRET
```

---

### Logs & Debugging

#### Tail Live Logs
```bash
wrangler tail

# Filter by status:
wrangler tail --status error
wrangler tail --status ok

# Sample rate:
wrangler tail --sampling-rate 0.5  # 50% of requests
```

**Example output:**
```
POST https://my-worker.dev/validate - Ok @ 10/7/2025, 6:55:43 PM
  └─ console.log: Token validated successfully
```

---

### Authentication

#### Login
```bash
wrangler login
# Opens browser for OAuth
```

#### Check Current Account
```bash
wrangler whoami
```

**Output:**
```
Account Name: Your Account
Account ID: abc123def456
Email: you@email.com
```

#### Logout
```bash
wrangler logout
```

---

## Configuration (`wrangler.toml`)

### Basic Configuration
```toml
name = "my-worker"
main = "src/index.js"
compatibility_date = "2025-10-07"

# KV Namespaces
kv_namespaces = [
  { binding = "SESSIONS", id = "abc123..." },
  { binding = "CACHE", id = "def456..." }
]

# Environment Variables (non-secret)
[vars]
ENVIRONMENT = "production"
API_URL = "https://api.example.com"
```

### Multiple Environments
```toml
name = "my-worker"
main = "src/index.js"

[env.staging]
name = "my-worker-staging"
kv_namespaces = [
  { binding = "SESSIONS", id = "staging-kv-id" }
]
vars = { ENVIRONMENT = "staging" }

[env.production]
name = "my-worker-production"
kv_namespaces = [
  { binding = "SESSIONS", id = "production-kv-id" }
]
vars = { ENVIRONMENT = "production" }
```

**Deploy to specific env:**
```bash
wrangler deploy --env staging
wrangler deploy --env production
```

### Routes (Custom Domains)
```toml
routes = [
  { pattern = "example.com/*", zone_name = "example.com" }
]

# Or multiple routes:
[[routes]]
pattern = "api.example.com/*"
zone_name = "example.com"

[[routes]]
pattern = "workers.example.com/*"
zone_name = "example.com"
```

---

## Common Workflows

### Initial Setup
```bash
# 1. Create project
mkdir my-worker && cd my-worker
npm init -y

# 2. Install wrangler
npm install -D wrangler

# 3. Initialize
npx wrangler init
# Creates wrangler.toml and src/index.js

# 4. Login
npx wrangler login

# 5. Create KV namespace
npx wrangler kv:namespace create "SESSIONS"
# Add returned ID to wrangler.toml

# 6. Deploy
npm run deploy
```

### Development Workflow
```bash
# 1. Start dev server
npm run dev

# 2. Test endpoints
curl http://localhost:8787/health

# 3. Make changes (hot reload automatic)

# 4. Deploy when ready
npm run deploy
```

### Debugging Production Issues
```bash
# 1. Tail logs
wrangler tail

# 2. Check recent deployments
wrangler deployments list

# 3. Rollback if needed
wrangler rollback

# 4. Check KV data
wrangler kv:key get --namespace-id=abc123 "sess:xyz"
```

---

## Troubleshooting

### Error: "KV namespace not bound"
```bash
# Check bindings in wrangler.toml
wrangler kv:namespace list

# Verify ID matches wrangler.toml
```

### Error: "Secret not found"
```bash
# List secrets
wrangler secret list

# Set missing secret
wrangler secret put SECRET_NAME
```

### Build Errors
```bash
# Test build without deploying
wrangler deploy --dry-run

# Check syntax locally
npm run dev
```

### Deployment Not Updating
```bash
# Force cache clear
wrangler deploy --no-bundle

# Check which version is live
wrangler deployments status
```

---

## Package.json Scripts (Recommended)

```json
{
  "scripts": {
    "dev": "wrangler dev",
    "deploy": "wrangler deploy",
    "deploy:staging": "wrangler deploy --env staging",
    "deploy:prod": "wrangler deploy --env production",
    "logs": "wrangler tail",
    "rollback": "wrangler rollback",
    "kv:list": "wrangler kv:key list --namespace-id=YOUR_KV_ID"
  }
}
```

**Usage:**
```bash
npm run dev
npm run deploy
npm run logs
```

---

## Advanced Features

### Cron Triggers (Scheduled Events)
```toml
[triggers]
crons = ["0 0 * * *"]  # Daily at midnight UTC
```

**Handle in code:**
```javascript
export default {
  async scheduled(event, env, ctx) {
    // Runs on schedule
    console.log('Cron triggered at', event.cron);
  }
};
```

### Durable Objects
```toml
[[durable_objects.bindings]]
name = "COUNTER"
class_name = "Counter"
script_name = "my-worker"
```

### Custom Builds
```toml
[build]
command = "npm run build"
watch_dirs = ["src", "lib"]
```

---

## Useful Tips

### Check What Will Be Deployed
```bash
wrangler deploy --dry-run --outdir=./dist
# Check dist/index.js to see bundled code
```

### Test with Different Compatibility Dates
```bash
wrangler dev --compatibility-date=2024-01-01
```

### View Full Deployment Details
```bash
wrangler deployments list --json | jq
```

### Quick Health Check
```bash
curl https://your-worker.workers.dev/health
```

---

## Resources

- **Official Docs:** https://developers.cloudflare.com/workers/wrangler/
- **API Reference:** https://developers.cloudflare.com/api/
- **Examples:** https://github.com/cloudflare/workers-sdk/tree/main/templates
- **Discord:** https://discord.cloudflare.com

---

## Cheat Sheet

| Task | Command |
|------|---------|
| Start dev | `wrangler dev` |
| Deploy | `wrangler deploy` |
| View logs | `wrangler tail` |
| Rollback | `wrangler rollback` |
| Set secret | `wrangler secret put NAME` |
| Create KV | `wrangler kv:namespace create "NAME"` |
| List deployments | `wrangler deployments list` |
| Check version | `wrangler --version` |
| Update wrangler | `npm install -D wrangler@latest` |

---

**Pro Tip:** Always test with `wrangler dev` before deploying to production! 🚀
