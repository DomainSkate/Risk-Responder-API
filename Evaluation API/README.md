# Evaluation API

This document will describe the specifications of the Evaluation API
Submitting an act request is free when you have a token, DomainSkate will contact you after your submission for the next steps.

### Authentication

- All APIs require Token Authentication.
- Please contact us to request/renew your token (via ayman@domainskate.com).
- Token should be passed in every API request via headers (see required headers).

### Required headers

```
Content-Type: application/json
Authorization: Token <your-token>
```

### Base URL

https://app.domainskate.com/api/v3.1/digital-identity

# APIs

Evaluation analyzes data points about each brand and tries to come up with a report that's easy to read and understand by business people. The process is slow due to the sheer size of it.

## Create API

**POST** `/create/`

|parameter    | Type                                | Is required | description                                                                                                                              |
|-------------|-------------------------------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------|
|domains      | Valid Domains (Separated by Commas) | Yes         | Analyzer depends on this as a starting point for its process.                                                                            |
|callback_url | Valid URL                           | Yes         | A webhook to be called back once the results are ready via a POST request.                                                               |
|keywords     | Array of Strings                    | No          |                                                                                                                                          |
|phrases      | Array of Strings                    | No          |                                                                                                                                          |
|owned_domains| Valid Domains (Separated by Commas) | No          | Owned_domains will eliminate the risk from these domains and it'll have a direct impact on the results’ total score and returned values. |
|customer     | String Identifier                   | Yes         | Any string. It will be used as an identifier for the customer.                                                                           |
|dryrun       | Boolean                             | No          | Generate random testing results, false by default.                                                                                       |

**Example of Valid Domains Separated by Commas for Request Body**

www.domainskate.com, www.domainskate.nyc, www.domainskate.us

**Example of a Valid URL for callback_url**

https://www.domainskate.com/

**Example request**

```bash
curl -X POST \
  https://app.domainskate.com/api/v3.1/digital-identity/create/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"dryrun": true,
	"customer": "domainskate",
	"callback_url": "https://app.domainskate.com/apis/v3.1/di/webhook/1/",
	"domains": ["domainskate.com", "domainskate.nyc"],
  "owned_domains": ["domainskate.com"]
}'
```

**Example response**

```bash
{
    "uuid": "d22ae5ea-4d45-4026-9dea-42b5ba588b01",
    "dryrun": true,
    "domains": [
        "domainskate.com",
        "domainskate.nyc"
    ],
    "keywords": null,
    "phrases": null,
    "owned_domains": ["domainskate.com"],
    "customer": "domainskate",
    "callback_url": "https://app.domainskate.com/apis/v3.1/di/webhook/1/",
    "created_on": "2020-11-03T12:59:33.862973Z"
}
```

## List results API

**GET** `/list/?page=1`

**Example request**

```bash
curl -X GET \
  'https://app.domainskate.com/api/v3.1/digital-identity/list/?page=1' \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json'
```

**Example response**

```json
{
    "count": 1,
    "next": "https://app.domainskate.com/api/v3.1/digital-identity/list/?page=2",
    "previous": null,
    "results": [
        {
            "uuid": "a9491ebf-633e-432b-b966-29f2bc7c3efe",
            "customer": "domainskate",
            "dryrun": true,
            "status": "Webhook done",
            "created_on": "2020-10-27T17:29:39.302728Z"
        }
    ]
}
```
**List Object**

|parameter    | Type              | description                                                                                                                                             |
|-------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
|uuid         | UUID              | Unique identifier of a result, store for later usage.                                                                                                   |
|customer     | String Identifier | Unique identifier for the user.                                                                                                                         |
|dryrun       | Boolean           | If test request.                                                                                                                                        |
|status       | Array of Strings  | Status of the report. Expect one of the following values:  "Queued" "In progress" "Processing done" "Processing Error"  "Webhook done" "Webhook failed" |
|created_on   | Datetime          | Date of creation.                                                                                                                                       |

## Details API

**GET** `/details/<uuid>/`

**Example request**

```bash
curl -X GET \
  https://app.domainskate.com/api/v3.1/digital-identity/details/3c52e439-0992-4f02-afe8-eefb6c1b638d/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json'
```

<aside>
🏁 On Mar 24, 2021, `theats` were introduced

</aside>

**Result Threats** 

| Name              |
|-------------------|
| Malware           |
| Phishing          |
| Risky Registrant  |
| Suspicious Domain |
| MX record         |


**Result Grades**

|Grade     |
|----------|
|High      |
|Medium    |
|Low       |

**Total Result Grades**

|Grade     |
|----------|
|A+        |
|A         |
|A-        |
|B+        |
|B         |
|B-        |
|C+        |
|C         |
|C-        |
|D+        |
|D         |


**Example response**

```json
{
  "uuid": "3c52e439-0992-4f02-afe8-eefb6c1b638d",
  "customer": "Domainskate",
  "dryrun": false,
  "status": "Webhook done",
  "created_on": "2021-03-24T12:31:03.581368Z",
  "domains": [
    "domainskate.com",
    "domainskate.nyc"
  ],
  "keywords": null,
  "phrases": null,
  "owned_domains": [
    "domainskate.com"
  ],
  "payload": {
    "total_grade": "B+",
    "customer": "Domainskate",
    "total_score": 87,
    "results": [
      {
        "result": "domainskate.kred",
        "grade": "Low",
        "explanation": [],
        "findings_message": "Suspicious domain name - exact brand name registered on domainskate.kred",
        "threats": [
          "Suspicious domain name"
        ]
      },
      {
        "result": "domainskart.in",
        "grade": "Low",
        "explanation": [
          {
            "recommendation": "Containment is a critical component for effectively dealing with risky domain names/websites that can be linked to your company or digital brand identity. Sites that are flagged should be reviewed and vetted for a source of confusion and fraud. Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
            "summary": "The domain name is registered in a country that has a greater risk of websites that are associated with fraudulent cyber activities",
            "description": "Domain names which originate in certain countries carry a greater risk of being used for cybercrime activities. These countries include China, Russia, Kenya, Nigeria, Vietnam, Indonesia, and Brazil. It is important that domain names and websites that originate from these countries are properly vetted, to ensure that they are not being used for malicious purposes"
          }
        ],
        "findings_message": "Risky Registration on domainskart.in",
        "threats": [
          "Risky registrant"
        ]
      },
      {
        "result": "domainstate.me",
        "grade": "Low",
        "explanation": [
          {
            "recommendation": "Containment is a critical component for effectively dealing with risky domain names/websites that can be linked to your company or digital brand identity. Sites that are flagged should be reviewed and vetted for a source of confusion and fraud. Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
            "summary": "The domain name may have an attached email server (MX Record). An MX Record (mail exchange) record is an entry in your DNS zone file which specifies a mail server to handle a domain's email. You must configure an MX record to receive email to your domain. This configuration may indicate that the site is being used to send/receive emails that could be used for cyber crime activities",
            "description": "Domain names which are attached to email servers, or MX Records, need to be properly vetted because they can be activated and used as a hub for email activities. A domain name or website that is not otherwise active, or being used to support a website that is public facing, can still be used as a hub for cybercrime if there is an email server attached to it"
          }
        ],
        "findings_message": "MX Record on domainstate.me, registered in Australia",
        "threats": [
          "MX record"
        ]
      }
    ],
    "uuid": "859cfb25-9751-4d59-8c59-4c95dc1715c6",
    "status": "Webhook done"
  },
  "callback_response_code": "200",
  "callback_response": "",
  "callback_triggered_on": "2021-03-24T12:35:37.649704Z"
}
```

## Update Owned/Ignored Domains API
*owned and ignored domains have the same effect*

**you must include all owned/ignored domains, even previously marked ones; this API assigns a fresh set of domains as owned/ignored with each call.**

**PUT** `/update-owned-domains/<uuid>/`

|parameter | Type             |Is required| description                                                                                                                            |
|----------|------------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------|
|owned_domains| Array of Strings |No         | owned/ignored domains will eliminate the risk from these domains and it'll have a direct impact on the brand's total score and results |

**Example request**

```bash
curl -X PUT \
  https://app.domainskate.com/api/v3.1/digital-identity/update-owned-domains/d22ae5ea-4d45-4026-9dea-42b5ba588b01/ \
  -H 'Authorization: Token <your-token>' \
  -H 'Content-Type: application/json' \
  -d '{
	"owned_domains": ["domainskate.com", "domainskate.nyc"]
}'
```

**Example response**

```json
{
   "owned_domains":[
      "domainskate.com",
      "domainskate.nyc"
   ],
   "payload":{
      "uuid":"3c52e439-0992-4f02-afe8-eefb6c1b638d",
      "total_grade":"C",
      "results":[
         {
            "grade":"High",
            "explanation":[
               {
                  "description":"<p><strong>RISKY ORIGIN: </strong>Domain names which originate in certain countries carry a greater risk of being used for cybercrime activities. These countries include China, Russia, Kenya, and _____. It is important that domain names and websites that originate from these countries are vetted, to ensure that they are not being used for malicious purposes.&nbsp;</p><p><strong>MX RECORD: </strong>Domain names which are attached to email servers, or MX Records, need to be properly vetted because they can be activated and used as a hub for email activities. A domain name or website that is not otherwise active, or being used to support a website that is public facing, can still be used as a hub for cybercrime if there is an email server attached to it.</p>",
                  "recommendation":"Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
                  "summary":"<p>The detected domain name has been flagged as having the following factors which may pose a risk to your digital identity:</p><ul><li>The domain name <a href=\"http://www.go.vivintsolar.com/\">www.go.vivintsolar.com/</a> is registered in a country that has a greater risk of websites that are associated with fraudulent cyber activities.</li><li>The domain name <a href=\"http://www.go.vivintsolar.com/\">www.go.vivintsolar.com/</a> has an attached email server (MX Record). An MX Record (mail exchange) record is an entry in your DNS zone file which specifies a mail server to handle a domain's email. You must configure an MX record to receive email to your domain. This configuration may indicate that the site is being used to send/receive emails that could be used for cyber crime activities.</li></ul>"
               }
            ],
            "result":"example.com",
            "findings_message":"Risky Registration on example.com"
         },
         {
            "grade":"Low",
            "explanation":[
               
            ],
            "result":"example.net",
            "findings_message":"High Traffic and Risky Registration on example.net, registered in Botswana"
         },
         {
            "grade":"Medium",
            "explanation":[
               {
                  "description":"Phishing is a computer-based cyber-attack that is used to obtain sensitive data. Typically, cyber-criminals will start a phishing attack by sending an email that looks like it originates from your company or brand. The authentic-looking—but fake—emails either request information from users or direct them to a fake website. If  the cyber-criminal is successful, the user will be lured to provide sensitive information, such as passwords, that allow the cyber-criminal to later impersonate the victim of the attack and gain access to your private accounts",
                  "recommendation":"A phishing attack compromises users and/or the public’s faith in the legitimacy of your business, and leads to significant losses for the affected persons. There is also substantial loss of time/resources as a result of dealing with complaints, and potential lost revenue stemming from the phishing activities. Phishing sites that are flagged should be reviewed and vetted for a source of confusion and fraud. Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
                  "summary":"The detected domain name has been flagged as a potential phishing site by our security protocols"
               },
               {
                  "description":"<p><strong>RISKY ORIGIN: </strong>Domain names which originate in certain countries carry a greater risk of being used for cybercrime activities. These countries include China, Russia, Kenya, and _____. It is important that domain names and websites that originate from these countries are vetted, to ensure that they are not being used for malicious purposes.&nbsp;</p><p><strong>MX RECORD: </strong>Domain names which are attached to email servers, or MX Records, need to be properly vetted because they can be activated and used as a hub for email activities. A domain name or website that is not otherwise active, or being used to support a website that is public facing, can still be used as a hub for cybercrime if there is an email server attached to it.</p>",
                  "recommendation":"Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
                  "summary":"<p>The detected domain name has been flagged as having the following factors which may pose a risk to your digital identity:</p><ul><li>The domain name <a href=\"http://www.go.vivintsolar.com/\">www.go.vivintsolar.com/</a> is registered in a country that has a greater risk of websites that are associated with fraudulent cyber activities.</li><li>The domain name <a href=\"http://www.go.vivintsolar.com/\">www.go.vivintsolar.com/</a> has an attached email server (MX Record). An MX Record (mail exchange) record is an entry in your DNS zone file which specifies a mail server to handle a domain's email. You must configure an MX record to receive email to your domain. This configuration may indicate that the site is being used to send/receive emails that could be used for cyber crime activities.</li></ul>"
               }
            ],
            "result":"example.org",
            "findings_message":"High Traffic on example.org, registered in China"
         }
      ],
      "status":"Queued",
      "total_score":70,
      "customer":"domainskate"
   }
}
```

## Limitations

⚠️ You are limited to run one Create API request per **month** per **customer.** To get monthly updates, you must call Create API on a monthly basis

# Webhook

**POST**

`callback_url`Will be called once the evaluation process is **done**

**Example payload**

```json
{
    "total_grade": "B+",
    "customer": "Domainskate",
    "total_score": 87,
    "results": [
      {
        "result": "domainskate.kred",
        "grade": "Low",
        "explanation": [],
        "findings_message": "Suspicious domain name - exact brand name registered on domainskate.kred",
        "threats": [
          "Suspicious domain name"
        ]
      },
      {
        "result": "domainskart.in",
        "grade": "Low",
        "explanation": [
          {
            "recommendation": "Containment is a critical component for effectively dealing with risky domain names/websites that can be linked to your company or digital brand identity. Sites that are flagged should be reviewed and vetted for a source of confusion and fraud. Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
            "summary": "The domain name is registered in a country that has a greater risk of websites that are associated with fraudulent cyber activities",
            "description": "Domain names which originate in certain countries carry a greater risk of being used for cybercrime activities. These countries include China, Russia, Kenya, Nigeria, Vietnam, Indonesia, and Brazil. It is important that domain names and websites that originate from these countries are properly vetted, to ensure that they are not being used for malicious purposes"
          }
        ],
        "findings_message": "Risky Registration on domainskart.in",
        "threats": [
          "Risky registrant"
        ]
      },
      {
        "result": "domainstate.me",
        "grade": "Low",
        "explanation": [
          {
            "recommendation": "Containment is a critical component for effectively dealing with risky domain names/websites that can be linked to your company or digital brand identity. Sites that are flagged should be reviewed and vetted for a source of confusion and fraud. Further recommended counter-measures and protocols can be found at https://www.resources.infosec.com",
            "summary": "The domain name may have an attached email server (MX Record). An MX Record (mail exchange) record is an entry in your DNS zone file which specifies a mail server to handle a domain's email. You must configure an MX record to receive email to your domain. This configuration may indicate that the site is being used to send/receive emails that could be used for cyber crime activities",
            "description": "Domain names which are attached to email servers, or MX Records, need to be properly vetted because they can be activated and used as a hub for email activities. A domain name or website that is not otherwise active, or being used to support a website that is public facing, can still be used as a hub for cybercrime if there is an email server attached to it"
          }
        ],
        "findings_message": "MX Record on domainstate.me, registered in Australia",
        "threats": [
          "MX record"
        ]
      }
    ],
    "uuid": "859cfb25-9751-4d59-8c59-4c95dc1715c6",
    "status": "Webhook done"
  }
```

**Limitations**

- Webhook will timeout in 30 seconds.
- Evaluation takes a couple of hours to finish.
