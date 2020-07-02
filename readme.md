# Logz.io Terraform provider

### Supports CRUD of Logz.io users, alerts and notification endpoints

This provider is based on the Logz.io client library - https://github.com/jonboydell/logzio_client

#### Requirements
Terraform 0.10.x

#### Obtaining the provider

The easiest way to get the provider is to run the `./scripts/update_plugin.sh` and edit the `PROVIDER_VERSION` variable to get the desired provider version. To get the latest:
```bash
bash <(curl -s https://raw.githubusercontent.com/logzio/logzio_terraform_provider/master/scripts/update_plugin.sh) 
```

#### Using the provider

**Note**: We provide multiple API endpoints by region, find your API host in [accounts & regions](https://docs.logz.io/user-guide/accounts/account-region.html#regions-and-urls). Default: api.logz.io

```hcl-terraform
provider "logzio" {
  api_token = "${var.api_token}"
  base_url = "${var.your_api_endpoint}" #e.g. https://api-au.logz.io
}
```

This simple example will create a Logz.io Slack notification endpoint (you'll need to provide the right URL) and an alert that
is triggered should Logz.io record 10 loglevel:ERROR messages in 5 minutes.  To make this example work you will also need to provide
your Logz.io API token.

```hcl-terraform
provider "logzio" {
  api_token = "${var.api_token}"
}

resource "logzio_endpoint" "my_endpoint" {
  title = "my_endpoint"
  description = "hello"
  endpoint_type = "slack"
  slack {
    url = "${var.slack_url}"
  }
}

resource "logzio_alert" "my_alert" {
  title = "my_other_title"
  query_string = "loglevel:ERROR"
  operation = "GREATER_THAN"
  notification_emails = []
  search_timeframe_minutes = 5
  value_aggregation_type = "NONE"
  alert_notification_endpoints = ["${logzio_endpoint.my_endpoint.id}"]
  suppress_notifications_minutes = 5
  severity_threshold_tiers {
      severity = "HIGH",
      threshold = 10
  }
}
```

See more of our examples at https://github.com/logzio/logzio_terraform_provider/tree/master/examples

#### Running the tests
`GO111MODULE=on TF_ACC=true go test -v .`

#### Build from source

#### Requirements
* Go 1.11

To build from the project root, this will copy it into your [plugins directory](https://www.terraform.io/docs/configuration/providers.html#third-party-plugins). 
```bash
./scripts/build.sh
```
**Note**: This build would work on Unix system, for other OSs, set the `GOOS` env variable before running the build script. For example:
```bash
export GOOS=windows
```

#### Changelog?

- v1.1.3 
    - examples now use TF12
    - will now generate the meta data needed for the IntelliJ type IDE HCL plugin
    - no more travis - just circle CI
    - version bump to use the latest TF library (0.12.6), now compatible with TF12
- 1.1.2 
    - Moved some of the source code around to comply with TF provider layout convention
    - Moved the examples into an examples directory

#### Doens't work?

Open an [issue](https://github.com/logzio/logzio_terraform_provider/issues).
Or fix it yourself and open a [PR](https://github.com/logzio/logzio_terraform_provider/pulls).

