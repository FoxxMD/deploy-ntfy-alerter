# Deploy a Gotify [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to [ntfy](https://ntfy.sh/). Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

## Requirements

* A running ntfy instance ([installation docs](https://docs.ntfy.sh/))
* A topic to push to

## Komodo Resource TOML

```toml
[[stack]]
name = "ntfy-alerter"
[stack.config]
repo = "foxxmd/deploy-ntfy-alerter"
file_paths = [
  "compose.yaml",
]
environment = """
  ## Required

  # Your Gotify instance URL
  NTFY_URL = https://ntfy.example.com

  # App Token created for Komodo
  NTFY_TOPIC = MyTopic

  ## Optional

  # Auth
  #NTFY_USER=[[NTFY_USER]]
  #NTFY_PASSWORD=[[NTFY_PASSWORD]]

  # Set the Gotify Priority level based on Komodo alert severity
  #NTFY_OK_PRIORITY=3
  #NTFY_WARNING_PRIORITY=5
  #NTFY_CRITICAL_PRIORITY=8
"""

[[variable]]
name = "NTFY_USER"
value = "MyUser"
is_secret = true

[[variable]]
name = "NTFY_PASSWORD"
value = "MyPass"
is_secret = true

[[alerter]]
name = "ntfy"
[alerter.config]
enabled = true
endpoint.type = "Custom"
endpoint.params.url = "http://ntfy-alerter-ip:7000"
```