# Deploy a ntfy [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to [ntfy](https://ntfy.sh/). Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

Supports optional authentication via query param, username/password, and access token.

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

  ## Your ntfy instance URL
  NTFY_URL = https://ntfy.example.com

  ## Topic to be used for for Komodo
  NTFY_TOPIC = MyTopic

  ## Optional

  ## Auth

  ## https://docs.ntfy.sh/publish/#username-password
  #NTFY_USER=[[NTFY_USER]]
  #NTFY_PASSWORD=[[NTFY_PASSWORD]]
  ## https://docs.ntfy.sh/publish/#access-tokens
  #NTFY_TOKEN=[[NTFY_TOKEN]]

  # Below need to add to `compose.yaml` as well

  ## Set the ntfy Priority level based on Komodo alert severity
  #NTFY_OK_PRIORITY=3
  #NTFY_WARNING_PRIORITY=5
  #NTFY_CRITICAL_PRIORITY=8

  # Set whether to include Komodo Severity Level in notification title
  #LEVEL_IN_TITLE=true

  # Prefixes messages with a checkmark when the Alert is in the 'Resolved' state
  #INDICATE_RESOLVED=true

  # Filter if an alert is pushed based on its Resolved status
  # * leave unset to push all alerts
  # * otherwise, alerts will only be pushed if Alert is one of the comma-separated states set here
  #ALLOW_RESOLVED_TYPE=resolved,unresolved
"""

[[variable]]
name = "NTFY_USER"
value = "MyUser"
is_secret = true

[[variable]]
name = "NTFY_PASSWORD"
value = "MyPass"
is_secret = true

[[variable]]
name = "NTFY_TOKEN"
value = "MyAccessToken"
is_secret = true

[[alerter]]
name = "ntfy"
[alerter.config]
enabled = true
endpoint.type = "Custom"
endpoint.params.url = "http://ntfy-alerter-ip:7000"
```