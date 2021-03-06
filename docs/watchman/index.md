
## What is Watchman

Moov Watchman is an HTTP API and Go library to download, parse and offer search functions over numerous trade sanction lists from the United States, European Union governments, agencies, and non profits for complying with regional laws. Also included is a web UI and async webhook notification service to initiate processes on remote systems.

[source: U.S. DEPARTMENT OF THE TREASURY](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/faq_general.aspx#basic)

![](images/webui.png)

## Running Moov Watchman

You can download a [binary from GitHub](https://github.com/moov-io/watchman/releases) or a [Docker image](https://hub.docker.com/r/moov/watchman) for Watchman. Once downloaded you can start making requests against Watchman. The service will download the latest data on startup.

```
$ docker run -p 8084:8084 -p 9094:9094 -it moov/watchman:v0.13.0
ts=2019-10-01T20:35:31.301254Z caller=main.go:54 startup="Starting watchman server version v0.13.0"
ts=2019-10-01T20:35:31.301338Z caller=database.go:18 database="looking for  database provider"
ts=2019-10-01T20:35:31.301376Z caller=sqlite.go:119 main="sqlite version 3.25.2"
ts=2019-10-01T20:35:31.302651Z caller=download.go:80 download="Starting refresh of data"
ts=2019-10-01T20:35:31.302651Z caller=main.go:118 admin="listening on :9094"
ts=2019-10-01T20:35:31.530729Z caller=download.go:132 download="Finished refresh of data"
ts=2019-10-01T20:35:31.532927Z caller=main.go:142 main="data refreshed - Addresses=11696 AltNames=9682 SDNs=7379 DeniedPersons=547"
ts=2019-10-01T20:35:31.532962Z caller=main.go:218 main="Setting data refresh interval to 12h0m0s (default)"
ts=2019-10-01T20:35:31.533312Z caller=main.go:182 startup="binding to :8084 for HTTP server"

$ curl -s localhost:8084/search?name=...
{
  "SDNs": [
    {
      "entityID": "...",
      "sdnName": "...",
      "sdnType": "...",
      "program": "...",
      "title": "...",
      "callSign": "...",
      "vesselType": "...",
      "tonnage": "...",
      "grossRegisteredTonnage": "...",
      "vesselFlag": "...",
      "vesselOwner": "...",
      "remarks": "..."
    }
  ],
  "altNames": null,
  "addresses": null
}
```

An SDN (or entity) is an individual, group, or company which has or could do business with United States companies or individuals. US law requires checking OFAC data before transactions.

## Web Interface

Moov Watchman provides a web interface for easy browsing of the SDN and related data for mobile and desktop clients. Simply load the address of Watchman in a browser.

## Webhooks

Watchman supports registering a callback url (also called [webhook](https://en.wikipedia.org/wiki/Webhook)) for searches or a given entity ID. (API docs: [company](https://api.moov.io/#operation/addCompanyWatch) or [customers](https://api.moov.io/#operation/addCustomerWatch)) This allows services to monitor for changes to the OFAC data. There's an example [app that receives webhooks](https://github.com/moov-io/watchman/blob/master/examples/webhook/webhook.go) written in Go. Watchman sends either a [Company](https://godoc.org/github.com/moov-io/watchman/client#OFacCompany) or [Customer](https://godoc.org/github.com/moov-io/watchman/client#OfacCustomer) model in JSON to the webhook URL.

Webhook URLs MUST be secure (https://...) and an `Authorization` header is sent with an auth token provided when setting up the webhook. Callers should always verify this auth token matches what was originally provided.

## FAQ

[FAQ](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/ques_index.aspx)

[General Questions](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/faq_general.aspx)

[Sanctions Compliance](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/faq_compliance.aspx)

[Sanction List and Files](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/faq_lists.aspx)

[Iran Sanctions](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/faq_iran.aspx)

[Other Sanction Programs](https://www.treasury.gov/resource-center/faqs/Sanctions/Pages/faq_other.aspx)
