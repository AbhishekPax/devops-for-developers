Markdown
# System and Application Logs

## Task
Search `journalctl` for all ERROR entries from a service in the last hour.

---

## 1. Searching journalctl for Errors in the Last Hour

### Primary Command
To query all entries with an error level (`err` / priority 3) or higher from a specific unit (e.g., `nginx.service`) logged within the previous hour:

```bash
sudo journalctl -u nginx.service -p err --since "1 hour ago"
Breakdown of Flags:
-u nginx.service: Targets the specific systemd service unit.

-p err (or -p 3): Filters by priority level (emerg [0], alert [1], crit [2], err [3]). Specifying err captures priority 0 through 3.

--since "1 hour ago": Restricts output to records generated within the past 60 minutes.

2. Additional Inspection Filters
Include Case-Insensitive Pattern Matching
Search for explicit "error" text patterns within the service logs over the last hour:

Bash
sudo journalctl -u nginx.service --since "1 hour ago" -g "(?i)error"
Follow Logs in Real Time with Error Filtering
Bash
sudo journalctl -u nginx.service -p err -f
Output in JSON or Detailed Format for Debugging
Bash
sudo journalctl -u nginx.service -p err --since "1 hour ago" -o verbose
3. Traditional Application Log Verification
For applications maintaining log files in /var/log:

Bash
# Check service error log entries
sudo tail -n 50 /var/log/nginx/error.log

# Check system syslog
sudo tail -n 50 /var/log/syslog


