# Act Request API

This document will describe the specifications of the Risk Responder API

### Authentication

- All APIs require Token Authentication.
- Please contact us to request/renew your token (via ayman@domainskate.com).
- Token should be passed through request headers

### Required Headers

```jsx
Authorization: Token <your-token>
```

### Base Url

https://app.domainskate.com/api/v1/

# APIs

Act Request allows users to submit a takedown request through our system with a set of predefined options.

**POST `/act/`**

| Parameter | Type | Is Required | Description |
| --- | --- | --- | --- |
| domain | String (Valid Domain) | Yes | Domain to take action against|
| threat_type | Integer | Yes | Degree of threat |
| threat_reason | Integer | Yes | Reason of report |

### Threat Types

| Type | Value |
| --- | --- |
| Alleged Violation | 0 |
| Confirmed Infringement | 1 |
| Unlawful Activities | 2 |
| Under Investigation | 3 |

### Threat Reasons

| Reason | Value |
| --- | --- |
| B2B Scam | 0 |
| Chargeback Money Recovery | 1 |
| Cryptocurrency | 2 |
| Dating & Romance | 3 |
| Employment | 4 |
| Fake IT & Tech Support | 5 |
| Illegal Products & Services | 6 |
| IP Infringement | 7 |
| Investment | 8 |
| Malware | 9 |
| Online Shopping | 10 |
| Phishing | 11 |
| Spam & Abuse | 12 |
| Subscription Scam | 13 |

### Example Request

**URL**

https://app.domainskate.com/api/v1/act/

**Headers**

```jsx
Authorization: Token <your-token>
```

Body

```jsx
{
	"domain": "www.domainskate.com",
	"threat_type": 0, // Alleged Violation
	"threat_reason": 0, // B2B Scam
}
```

### Example Responses

**Submitted successfully**

```jsx
{
    "message": "Act request for www.domainskate.com was submitted successfully"
}
```

**Request already exists for a specific domain**

```jsx
{
    "message": "Act request for www.domainskate.com is already submitted"
}
```

**A parameter is missing**

```jsx
{
    "threat_reason": [
        "This field is required."
    ]
}
```

**No authorization credentials**

```jsx
{
    "detail": "Authentication credentials were not provided."
}
```

**Incorrect authorization credentials**

```jsx
{
    "detail": "Invalid token."
}
```
