#!/bin/sh
# Interface State Change Monitor for OPNsense / FreeBSD
# Monitors network interfaces for up/down changes and sends Discord alerts
# Only sends when a change is detected
# Runs under cron
#----------------------------------------------------------------------------

PATH=/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin
# Discord webhook URL (replace with your own)
WEBHOOK_URL=""

# File where last known states are stored for comparison (replace with your own)
STATUS_FILE=""

# Hostname to show in alerts (auto-detected)
HOSTNAME=$(hostname)

# Timezone for timestamps
TZ_ZONE="America/Chicago"

# Function: now() Returns current time in the chosen timezone.
#   The TZ path ensures correct behavior on BSD systems
now() {
  TZ="/usr/share/zoneinfo/$TZ_ZONE" date +"%Y-%m-%d %H:%M:%S %Z"
}

# Function: send_discord()
# Sends a text message to the configured Discord webhook URL.
send_discord() {
  curl -s -H "Content-Type: application/json" \
       -X POST \
       -d "{\"content\": \"$1\"}" \
       "$WEBHOOK_URL" >/dev/null 2>&1
}

# Main Loop: goes over all interfaces, check state and then compare with last run.
/sbin/ifconfig -l | tr ' ' '\n' | while read iface; do

# Get current state info for the interface
  info=$(/sbin/ifconfig "$iface")

# Determine up/down state
  if echo "$info" | /usr/bin/grep -q "inet " && echo "$info" | /usr/bin/grep -q "UP"; then
    state="up"
  else
    state="down"
  fi

  ts=$(now)
  entry="${iface}:${state}:${ts}"

# If first run, create the status file with current state and skip alerting
  if [ ! -f "$STATUS_FILE" ]; then
    echo "$entry" >> "$STATUS_FILE"
    continue
  fi

# Get the previous state for this interface
  prev_line=$(/usr/bin/grep "^${iface}:" "$STATUS_FILE")
  prev_state=$(echo "$prev_line" | /usr/bin/cut -d':' -f2)

# If no record exists yet for this iface, add it and skip alerting
  if [ -z "$prev_state" ]; then
    echo "$entry" >> "$STATUS_FILE"
    continue
  fi

# If the state has changed since last run, update the file and send alert
  if [ "$prev_state" != "$state" ]; then
    /usr/bin/sed -i '' "s|^${iface}:.*|${entry}|" "$STATUS_FILE"
    send_discord "**[$HOSTNAME]** Interface \`${iface}\` changed FROM: \`${prev_state}\` TO: \`${state}\` @ ${ts}"
  fi
done
