INCIDENT NOTES: Manual Chaos Test (Project 24)



SUMMARY

A planned manual failure was injected by stopping the nginx web service on

a Linux VM (Ubuntu 24.04). Recovery was performed manually and verified

with HTTP checks and service logs.



TIMELINE (IST, 23 Nov 2026)

13:32:35  nginx stopped with: sudo systemctl stop nginx

13:32:35  systemctl is-active reported "inactive"; health-check watcher

&#x20;         began showing DOWN / 000

13:33:04  nginx started with: sudo systemctl start nginx

13:33:04  systemctl is-active reported "active"

13:33:05  curl returned HTTP/1.1 200 OK; watcher returned to 200



IMPACT

Web service unavailable on port 80 for about 29 seconds. No data loss

(static default page only).



DETECTION

A shell loop curled http://localhost every second and printed the HTTP

status code. The change from 200 to DOWN/000 showed the outage immediately.



ROOT CAUSE

Intentional: the service was stopped manually as part of a chaos test.



RECOVERY STEPS

1\. Confirmed the service was inactive: systemctl is-active nginx

2\. Started the service: sudo systemctl start nginx

3\. Verified active state and HTTP 200 response

4\. Confirmed in logs: journalctl -u nginx



VERIFICATION

\- systemctl is-active nginx -> active

\- curl -I http://localhost -> HTTP/1.1 200 OK

\- journalctl shows Stopped at 08:02:35 UTC and Started at 08:03:04 UTC



LESSONS LEARNED

\- A simple 1-second health-check loop gives fast outage detection.

\- systemd makes manual recovery a one-command action.

\- Production setups should add automatic restart (Restart=on-failure in the

&#x20; systemd unit) and external monitoring/alerting so recovery does not

&#x20; depend on a person.



EVIDENCE

Screenshot 1: outage (inactive, DOWN in watcher)

Screenshot 2: recovery (active, 200 OK, watcher back to 200)

Screenshot 3: journalctl output

