# interface status update

This script monitors all interfaces by parsing 'ifconfig' output and checking for changes in the status based on up flags. When a change is detected ( interface goes up or down), it sends an alert to Discord.

\
The script stores previous interface states in where you specify and compares them on each run. I chose to execute this with a cronjob where the script runs every 30 minutes each day.



