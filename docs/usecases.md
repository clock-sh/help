## Watch Your HTTPS Cert

```Python3
Check if a SSL cert will expire in 3 days.

Note:
Clock.sh's time limit is 3min.
"""
import sys
from urllib.request import ssl, socket
from datetime import datetime, timedelta

today = datetime.now()

# You may want to change this
hostnames = [
    "kawabangga.com",
    "clock.sh"
]
port = "443"
alert_alive_days = 3


def check_host_cert(hostname):
    context = ssl.create_default_context()
    with socket.create_connection((hostname, port)) as sock:
        with context.wrap_socket(sock, server_hostname=hostname) as ssock:
            j = ssock.getpeercert()

    expire_str = j["notAfter"]
    expire_date = datetime.strptime(expire_str, "%b %d %I:%M:%S %Y %Z")
    cert_live_time = expire_date - today
    print("{} will expire in {}".format(hostname, cert_live_time))
    ok = cert_live_time > timedelta(days=3)
    return ok


results = [check_host_cert(hostname) for hostname in hostnames]

if all(results):
    sys.exit(0)
else:
    sys.exit(1)
```
