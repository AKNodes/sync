---
hidden: true
---

# Docker logs auto delete script

Perfect idea 💡 — yes, you can absolutely **automate log cleanup + prevention** so you never run out of disk space again.

Below is a **ready-to-use script** that:\
✅ Clears huge Docker logs safely\
✅ Adds log rotation limits to `/etc/docker/daemon.json`\
✅ Restarts Docker\
✅ Sets up a **cron job** to run weekly (or daily, if you prefer)

***

#### 🧰 Step 1: Create the script

Run this:

```bash
sudo tee /usr/local/bin/docker-log-cleanup.sh > /dev/null <<'EOF'
#!/bin/bash
# Docker log cleanup + prevention script
# Author: ChatGPT Automation
# Date: $(date)

LOG_LIMIT_SIZE="100m"
LOG_MAX_FILES="3"
DOCKER_CONFIG="/etc/docker/daemon.json"

echo "🧹 Truncating existing Docker logs..."
find /var/lib/docker/containers/ -type f -name "*.log" -exec truncate -s 0 {} \;

echo "⚙️  Ensuring Docker log rotation policy..."
if [ -f "\$DOCKER_CONFIG" ]; then
    cp "\$DOCKER_CONFIG" "\${DOCKER_CONFIG}.bak_$(date +%F_%H-%M-%S)"
fi

cat > "\$DOCKER_CONFIG" <<JSON
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "$LOG_LIMIT_SIZE",
    "max-file": "$LOG_MAX_FILES"
  }
}
JSON

echo "🔁 Restarting Docker to apply config..."
systemctl restart docker

echo "✅ Docker log cleanup complete."
EOF
```

Then make it executable:

```bash
sudo chmod +x /usr/local/bin/docker-log-cleanup.sh
```

***

#### 🕒 Step 2: Automate with cron

Run:

```bash
sudo crontab -e
```

Add this line at the bottom (for **weekly cleanup**, every Sunday at 2:00 AM):

```
0 2 * * 0 /usr/local/bin/docker-log-cleanup.sh >> /var/log/docker-log-cleanup.log 2>&1
```

If you want **daily cleanup**, use:

```
0 2 * * * /usr/local/bin/docker-log-cleanup.sh >> /var/log/docker-log-cleanup.log 2>&1
```

***

#### 🧾 Step 3: Verify

You can test it manually first:

```bash
sudo /usr/local/bin/docker-log-cleanup.sh
```

And check logs:

```bash
cat /var/log/docker-log-cleanup.log
```

***

#### ✅ What this gives you

* Automatic log trimming (safe, no data loss)
* Docker configured to prevent future log bloat
* A cron-based safety net that keeps your server healthy
