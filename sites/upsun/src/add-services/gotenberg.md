---
title: "Gotenberg"
weight: -95
description: Gotenberg is a user-friendly API for PDF files.  
---

Gotenberg is a stateless API for converting various document formats into PDF files.
For more information, see the [Gotenberg documentation](https://gotenberg.dev/docs/getting-started/introduction).

## Supported versions

- 8

{{% major-minor-versions-note configMinor="false" %}}

{{% relationship-ref-intro %}}

{{< codetabs >}}
+++
title= Service environment variables
+++

{{% service-values-change %}}

```bash
GOTENBERG_INSTANCE_IPS=["249.45.240.83"]
GOTENBERG_SCHEME=http
GOTENBERG_HOSTNAME=rssjaxyeoorjje6axcq35xu5gq.gotenberg.service._.eu-5.{{< vendor/urlraw "hostname" >}} >}}
GOTENBERG_SERVICE=gotenberg
GOTENBERG_CLUSTER=p2f2xrzyq7a6k-main-bvxea6i
GOTENBERG_IP=169.254.137.3
GOTENBERG_TYPE=gotenberg:8
GOTENBERG_HOST=gotenberg.internal
GOTENBERG_PORT=3000
GOTENBERG_REL=http
```

<--->

+++
title= `PLATFORM_RELATIONSHIPS` environment variable
+++

For some advanced use cases, you can use the [`PLATFORM_RELATIONSHIPS` environment variable](/development/variables/use-variables.md#use-provided-variables).
The structure of the `PLATFORM_RELATIONSHIPS` environment variable can be obtained by running `{{< vendor/cli >}} relationships` in your terminal:

```json
{
      "host": "gotenberg.internal",
      "hostname": "rssjaxyeoorjje6axcq35xu5gq.gotenberg.service._.eu-5.{{< vendor/urlraw "hostname" >}}",
      "cluster": "p2f2xrzyq7a6k-main-bvxea6i",
      "service": "gotenberg",
      "rel": "http",
      "scheme": "http",
      "port": "3000",
      "type": "gotenberg:8",
      "instance_ips": [
      "249.45.240.83"
      ],
      "ip": "169.254.137.3",
      "url": "http://gotenberg.internal:3000"
    }
```

Here is an example of how to gather [`PLATFORM_RELATIONSHIPS` environment variable](/development/variables/use-variables.md#use-provided-variables) information
in a [`.environment` file](/development/variables/set-variables.md#use-env-files):

```bash {location=".environment"}
# Decode the built-in credentials object variable.
export RELATIONSHIPS_JSON=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode)

# Set environment variables for individual credentials.
export APP_GOTENBERG_HOST=="$(echo $RELATIONSHIPS_JSON | jq -r '.gotenberg[0].host')"
```

{{< /codetabs >}}

## Usage example

### 1. Configure the service

To define the service, use the `gotenberg` type:

```yaml {configFile="app"}
services:
    # The name of the service container. Must be unique within a project.
    <SERVICE_NAME>:
        type: gotenberg:<VERSION>
```

Note that changing the name of the service replaces it with a brand new service and all existing data is lost. Back up your data before changing the service.

### 2. Add the relationship

To define the relationship, use the ``http`` endpoint:

```yaml {configFile="app"}
applications:
    # The name of the app container. Must be unique within a project.
    <APP_NAME>:
        # Relationships enable access from this app to a given service.
        relationships:
            <RELATIONSHIP_NAME>: "<SERVICE_NAME>:http"

services:
    # The name of the service container. Must be unique within a project.
    <SERVICE_NAME>:
        type: gotenberg:<VERSION>
```

You can define ``<SERVICE_NAME>`` as you like, so long as it’s unique between all defined services and matches in both the application and services configuration.

With the above definition, {{% vendor/name %}} uses the `http` endpoint,
providing a [relationship](/create-apps/app-reference/single-runtime-image#relationships) (the network address a service is accessible from) that is identical to the _name_ of that service.

The `http` endpoint uses port `3000` by default.

### Example configuration

```yaml {configFile="app"}
applications:
    # The name of the app container. Must be unique within a project.
    myapp:
        # Relationships enable access from this app to a given service.
        relationships:
            gotenberg: "gotenberg:http"

services:
    # The name of the service container. Must be unique within a project.
    gotenberg:
        type: gotenberg:8
```

## Generate a PDF using Gotenberg

As an example, to generate a PDF file of the {{% vendor/name %}} website, run the following cURL command:

```bash {location="Terminal"}
curl \
--request POST http://yduimhaby523ase4lju3qhimre.gotenberg8.service._.eu-3.{{< vendor/urlraw "hostname" >}}/forms/chromium/convert/url \
--form url=https://upsun.com \
--form landscape=true \
--form marginTop=1 \
--form marginBottom=1 \
-o my.pdf
```