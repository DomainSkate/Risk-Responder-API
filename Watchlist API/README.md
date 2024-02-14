# Watchlist API

This document will describe the specifications of the Watchlist API.

### Authentication

- All APIs require Token Authentication.
- Please contact us to request/renew your token (via systems@domainskate.com).
- Token should be passed in every API request via headers (see required headers).

### Required headers

```
Content-Type: application/json
Authorization: Token <your-token>
```

## Base URLs

### Watchlist

https://app.domainskate.com/api/v1/watchlist/

### Webhooks

https://app.domainskate.com/api/v1/watchlist/webhooks/

# APIs

Watchlist allows users to add domains into a monitored list, any changes detected by our system for these domains will be immediately reported to the user.
Changes include: Website information, website screenshot, website threat evaluation and website whois information including raw whois data.

# Watchlist APIs

## Create API

**POST** `/create/`

| parameter | Type                  | Is required | description                        |
|-----------|-----------------------|-------------|------------------------------------|
| domains   | List of Valid Domains | Yes         | Domains to add into the watchlist. |

**Example request**

```bash
curl -X POST \
  https://app.domainskate.com/api/v1/watchlist/create/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"domains": ["https://www.domainskate.com", "https://www.domainskate.nyc"]
}'
```

**Example response**

```json
{
    "success": [{
      "uuid": "Domain UUID",
      "url": "Domain URL"
    }],
    "invalid_urls": "list of possible invalid formatted urls.",
    "duplicate_urls": "list of already existing domains in your watchlist.",
    "maximum_domains_error": "list of domains unaccepted due to maximum limit."
}
```

## List results API

**GET** `/list/`

**Example request**

```bash
curl -X GET \
  'https://app.domainskate.com/api/v1/watchlist/list/' \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json'
```

**Example response**

```json
{
  "watchlist": [
    {
      "uuid": "UUID",
      "url": "domainskate.com"
    },
    {
      "uuid": "UUID",
      "url": "domainskate.net"
    }
  ]
}
```

## Details API

**GET** `/details/`

**Example request**

```bash
curl -X GET \
  https://app.domainskate.com/api/v1/watchlist/details/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"uuid": DomainUUID,
  }'
```

This also accepts an optional "date" with the following format: YYYY-MM-DD

**Example response**

```json
{
  "uuid": "UUID",
  "created": "2024-02-13T23:47:17.653",
  "url": "cnn.com",
  "ip_address": "172.16.75.114",
  "mx_records": "mxb-00241e02.gslb.pphosted.com. mxa-00241e02.gslb.pphosted.com.",
  "dns_records": "151.101.3.5, 151.101.131.5, 151.101.195.5, 151.101.67.5",
  "screenshot": {
    "captured_date": "2024-02-13",
    "page_title": "",
    "screenshot_changed": false,
    "chain_of_redirects": "",
    "total_redirects": 0,
    "is_parked": "",
    "is_for_sale": "",
    "is_blank": "",
    "metadata_keywords": "",
    "metadata_description": "",
    "social_media_links": {
      "facebook": "",
      "twitter": "",
      "instagram": "",
      "youtube": "",
      "telegram": ""
    },
    "og_image": "",
    "website_screenshot": ""
  },
  "evaluation": {
    "evaluation_date": "2024-02-14",
    "website_type": "News and Media",
    "threats": "",
    "evaluation_changed": false
  },
  "whois": {
    "domain_created": "1993-09-22",
    "domain_updated": "2023-12-06",
    "domain_expires": "2027-09-21",
    "registrar": "NOM-IQ Ltd dba Com Laude",
    "name_servers": "ns-1086.awsdns-07.org, ns-1630.awsdns-11.co.uk, ns-47.awsdns-05.com, ns-576.awsdns-08.net",
    "whois_changed": true,
    "domain_status": "Domain Status",
    "registrant_company": "Turner Broadcasting System, Inc.",
    "registrant_state": "GA",
    "registrant_country": "United States",
    "registrant_email": "cnn.com-Registrant@anonymised.email",
    "admin_company": "REDACTED FOR PRIVACY",
    "admin_state": "REDACTED FOR PRIVACY",
    "admin_country": "REDACTED FOR PRIVACY",
    "admin_email": "cnn.com-Admin@anonymised.email",
    "raw_whois": "Raw Whois Data"
  }
}
```

## Delete API

**DELETE** `/delete/`

| parameter | Type          | Is required | description                                           |
|-----------|---------------|-------------|-------------------------------------------------------|
| uuid      | List of UUIDs | Yes         | Result UUIDs, can be extracted from the `/list/` call |

**Example request**

```bash
curl -X DELETE \
  https://app.domainskate.com/api/v1/watchlist/delete/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"uuid": ["uuid1", "uuid2"]
}'
```

**Example response**

```json
{
  "success": [
    {
      "uuid": "UUID",
      "domain": "domainskate.com"
    }
  ],
  "failed": []
}
```

# Webhook APIs

Webhooks will be contacted when changes are detected in our scope of detection. For additional details, see [Detected Changes Explanation](https://github.com/DomainSkate/Risk-Responder-API/tree/main/Watchlist%20API#detected-changes-explanation).

## Create API

**POST** `/create/`

Adding a webhook

**Example request**

```bash
curl -X POST \
  https://app.domainskate.com/api/v1/watchlist/webhooks/create/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"url": "Valid URL"
  }'
```

**Example payload**

```json
{
  "uuid": "uuid",
  "created_date": "date",
  "url": "domain url",
  "detected_change": ""
}
```

### Detected Changes Explanation
Detected change will vary depending on where exactly the change occurred; our system detects and monitors different sections for each result here are the possible changes:

**Threat Evaluation Change**

```json
{
  "uuid": "uuid",
  "created_date": "date",
  "url": "domain url",
  "detected_change": "Evaluation"
}
```

**Screenshot/Website Information Change**
```json
{
  "uuid": "uuid",
  "created_date": "date",
  "url": "domain url",
  "detected_change": "Screenshot",
  "list_of_changes": "This will contain which fields changed, all possible changes listed below"
}
```
**Possible changes in fields for Screenshot**

Page Title, Redirects, Special Tags (parked, for-sale, blank), Metadata Keywords, Metadata Description, OG Image, Social Media Links, Screenshot Image.

**Whois Change**
```json
{
  "uuid": "uuid",
  "created_date": "date",
  "url": "domain url",
  "detected_change": "Whois"
}
```

## Delete API
**DELETE** `/delete/`

**Example request**

```bash
curl -X DELETE \
  https://app.domainskate.com/api/v1/watchlist/webhooks/delete/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"uuid": "webhook UUID",
  }'
```

## List API
**GET** `/list/`

**Example request**

```bash
curl -X GET \
  https://app.domainskate.com/api/v1/watchlist/webhooks/list/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json'
```

**Example response**
```json
{
    "webhooks": [
        {
            "uuid": "UUID",
            "url": "url"
        }
    ]
}
```
### Limitations

- Webhook will retry 3 times, once every 30 minutes. Companies can only assign 3 total webhooks.
