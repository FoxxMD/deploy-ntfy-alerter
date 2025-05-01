# Deploy a ntfy [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to [ntfy](https://ntfy.sh/). Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

Supports optional authentication via query param, username/password, and access token.

## Requirements

* A running ntfy instance ([installation docs](https://docs.ntfy.sh/))
* A topic to push to

## Deploying

Use **either** of the methods below.

### Komodo Resource Sync

<details>

Create a [Resource Sync](https://komo.do/docs/sync-resources) with the TOML configuration below to

* create the [Stack](https://komo.do/docs/resources#stack) to run deploy-ntfy-alerter
* add the Ntfy User/Password or Token [Variable](https://komo.do/docs/variables) and
* setup the [Alerter](https://komo.do/docs/resources#alerter) + configuration

Steps:

* Open Komodo Dashboard -> Syncs -> **New Resource Sync**
* Choose Mode -> UI Defined
  * Toggle the following to active:
    * Managed
    * Include Sync Resources
    * Include Sync Variables

Add the below configuration to **Resource File** field and then modify variables for your environment (NTFY_URL, endpoint.params.url, etc...)

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

  ## Delay alerts with below types for X milliseconds 
  ## and cancel pushing alert if it is resolved within that time
  #UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
  #UNRESOLVED_TIMEOUT=2000
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

**Save** the sync and then **Execute Sync** to create the Alerter.

</details>

## Manual setup

<details>

Create a new [**Stack**](https://komo.do/docs/resources#stack) with the following for `compose.yaml` file.

```yaml
services:
  komodo-ntfy:
    image: foxxmd/komodo-ntfy-alerter:latest
    restart: unless-stopped
    env_file:
      - ./.env
    ports:
      - "7000:7000"
```

Add the following to the Stack -> Config -> Environment section:

```ini
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

## Delay alerts with below types for X milliseconds 
## and cancel pushing alert if it is resolved within that time
#UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
#UNRESOLVED_TIMEOUT=2000
```

Make sure to replace placeholder values. `[[NTFY_USER]]` is a Komodo [Variable](https://komo.do/docs/variables).

After deploying the Stack create a new [Alerter](https://komo.do/docs/resources#alerter)

* **Endpoint:** `Custom`
  * In the Endpoint field set the IP:PORT of the `komodo-ntfy-alerter` stack you created IE `http://192.168.YOUR.IP:7000`
* Optionally, set any **Alert Types** you may need

**Save** the Alerter and then **Test Alerter** to make sure everything is ready to use.

</details>