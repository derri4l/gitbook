# interface status update

This script monitors all interfaces by parsing 'ifconfig' output and checking for changes in the status based on up flags. When a change is detected ( interface goes up or down), it sends an alert to Discord.

\
The script stores previous interface states in where you specify and compares them on each run. I chose to execute this with a cronjob where the script runs every 30 minutes each day.

<details>

<summary>readme</summary>

````
/# Interface State Change Monitor
This script monitors network interface status changes on OPNsense/FreeBSD systems and sends Discord alerts when a state change is detected. It is designed to run under cron and only sends notifications when there is a change.

## Features
- Monitors interface up/down status
- Compares current state with previous state stored in a file
- Sends Discord notifications when a change is detected

## Prerequisites
- A Unix-like environment (OPNsense/FreeBSD) with `ifconfig`, `grep`, `sed`, and `curl` installed.
- A valid Discord webhook URL. Update the `WEBHOOK_URL` variable in the script with your Discord webhook.
- A designated file for storing interface statuses. Set the `STATUS_FILE` variable to a valid writable path.

## Configuration
1. **Set Discord Webhook URL**: 
   Replace the empty string in the variable `WEBHOOK_URL` with your Discord webhook URL.

2. **Set Status File Path**:
   Specify a full path for `STATUS_FILE` where the script can read/write the last known states.

3. **Timezone**:
   Adjust the `TZ_ZONE` variable if needed for your system's timezone (default is set to `America/Chicago`).

## Usage
- **Run Manually**: You can execute the script manually to check interfaces:
  ```sh
  ./int_monitor
  ```

- **Run via Cron**: Schedule the script to run periodically. For example, add the following line to your crontab:
  ```cron
  */5 * * * * /path/to/int_monitor
  ```

## How It Works
- The script retrieves the current state of each network interface using `ifconfig`.
- It then compares each interface's state with the previously recorded state in `STATUS_FILE`.
- If a state change is detected, the script updates the status file and sends an alert to your configured Discord channel.

## Troubleshooting
- **No Alerts**: Ensure the webhook URL and status file path are set correctly.
- **Permissions**: Verify that the script has execute permissions and that the status file is writable.
- **Dependencies**: Confirm that all required commands (`ifconfig`, `grep`, `sed`, and `curl`) are available on your system.


````

</details>

<details>

<summary>script</summary>

```sh
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
```

</details>

<details>

<summary>media</summary>

<figure><img src="../../../.gitbook/assets/Screenshot 2025-08-18 012137.png" alt=""><figcaption><p>manuallly turned off vlan40 and tailscale, and then turned vlan20 back up</p></figcaption></figure>

</details>
