# UniqueSignals - programmatic access API docs

## Access to `uniquesignals` API

To get access to the programmatic API od the `uniquesignals` system you must:

1. Register your organization on [Unique Signals Portal](https://uniquesignals.ai/auth/signup)
2. Upgrade to the [PRO Plan](https://uniquesignals.ai/pricing)
3. Request API Key [todo]
4. Start using the system!

## Example API usage

### Using CURL

#### 1. Initiate processing

```bash
curl -X POST "https://uniquesignals.io/api/get-signals" \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your-api-key-generated-with-uniquesignals",
    "business_type": "insurance",
    "service_description": "fire insurance",
    "lng": -122.4194,
    "lat": 37.7749,
    "radius_m": 10000
  }'
```

You will get the following response:

```json
{
  "job_id": "b93b1d73-ee50-43db-b0e3-b57d00c820f4",
  "job_status": "running",
  "organization_id": "unique-id-of-your-organization",
  "error_message": null,
  "started_at": "2026-01-28T07:48:39.756001+00:00",
  "completed_at": null,
  "url": null,
  "estimated_processing_time_s": 30,
  "n_records": 0,
  "n_signals": 0,
  "total_token_count": null
}

```

#### 2. Get link to the data

After around 30 seconds you can query again the same endpoint, but this time using returned `job_id` parameter. If job is completed you will get a link valid for 60 seconds from where you can initiate download:

```bash
curl -X POST "https://uniquesignals.io/api/get-signals" \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your-api-key-generated-with-uniquesignals",
    "job_id": "b93b1d73-ee50-43db-b0e3-b57d00c820f4"
  }'
```

And then you get the final response with the link:

```json
{
  "job_id": "b93b1d73-ee50-43db-b0e3-b57d00c820f4",
  "job_status": "completed",
  "organization_id": "unique-id-of-your-organization",
  "error_message": null,
  "started_at": "2026-01-28T07:48:39.756001+00:00",
  "completed_at": "2026-01-28T07:49:25.481307+00:00",
  "url": "https://.../signals/b93b1d73-ee50-43db-b0e3-b57d00c820f4.json?...&Expires=1769586634",
  "estimated_processing_time_s": 30,
  "n_records": 159,
  "n_signals": 471,
  "total_token_count": 63991
}
```

#### 3. Download data

Use this link to download the data:

```bash
wget "https://.../signals/b93b1d73-ee50-43db-b0e3-b57d00c820f4.json?...&Expires=1769586634"
```

```json
{
  "businesses": {
    "biz_0": {
      "address": "SOUTH, 320 Wall St F, Los Angeles, CA 90013",
      "name": "MJ Wholesale",
      "website": null,
      "phone": "(213) 670-7652",
      "email": null,
      "uq_reasoning": "Businesses that hold large inventories of goods in warehouses are highly susceptible to fire. Wholesale trade entities need to protect their stock and facilities.",
      "signal_ids": [
        "hazmat#la_fire_INC1982_2f84e20a"
      ]
    },
    "biz_1": {
      "address": "340 San Pedro St # G, Los Angeles, CA 90013",
      "name": "ABC Wholesale Merchandise",
      "website": null,
      "phone": "(213) 687-6505",
      "email": null,
      "uq_reasoning": "Businesses that hold large inventories of goods in warehouses are highly susceptible to fire. Wholesale trade entities need to protect their stock and facilities.",
      "signal_ids": [
        "hazmat#la_fire_INC1982_2f84e20a"
      ]
    },
    ...  },
  "signals": {
    "hazmat#la_fire_INC1982_2f84e20a": {
      "signal_type": "fire_emergency",
      "address": null,
      "city": "Los Angeles",
      "state": "CA",
      "description": "Lafd hazmat is investigating light smoke emanating from a shipping container. The container is not on a vessel.",
      "event_time": "2026-01-15T00:38:00.000000",
      "reasoning": "Wholesale trade: Negative impact from disruption of supply chains and inability to transport or store contaminated goods. Positive impact for wholesalers of hazmat response equipment and cleanup supplies.",
      "signal_metadata": {
        "signal_class": "public safety",
        "signal_group": null,
        "severity": "medium",
        "neighborhood": "1982",
        "district": null,
        "zipcode": null,
        "jurisdiction": "Los Angeles Fire Department",
        "title": null
      },
      "business_ids": [
        "biz_0",
        "biz_81",
        "biz_82",
        "biz_83",
        "biz_84"
      ]
    },
    "vvG1iZbwpOndBI": {
      "signal_type": "live events",
      "address": "5151 State University Drive",
      "city": "Los Angeles",
      "state": "California",
      "description": "Music event: Chiquis. Event genre: Latin. Event class: Latin.",
      "event_time": "2026-03-08T03:00:00Z",
      "reasoning": "Wholesale trade: Positive impact due to increased demand for related merchandise (e.g., merchandise, costumes, decorations) before and after the event, though the primary impact is on retail.",
      "signal_metadata": {
        "signal_class": "live events",
        "genre": "Latin",
        "segment": "Music",
        "sub_genre": "Latin",
        "venues_name": "Luckman Fine Arts Complex",
        "event_name": "Chiquis"
      },
      "business_ids": [
        "biz_3",
        "biz_4",
        "biz_5",
        "biz_6",
        "biz_7",
        "biz_8",
        "biz_11",
        "biz_12",
        "biz_85"
      ]
    },
    ...  },
  "business_no_signals": [],
  "job_id": "b93b1d73-ee50-43db-b0e3-b57d00c820f497136f8e-6f37-4129-a08e-782302ac2f14",
  "recommendation_score": 7
}
```

### Using Python

**Note**: soon `uniquesignals.io` will have dedicated Python client!

When using Python it is easier to automate data retrieval. Below you have a full script that is using `requests` package and `uniquesignals.io` api.

```python
import os
import time

import requests


BUSINESS_TYPE = 'insurance'
LAT = 34.05223
LNG = -118.24368
RADIUS_M = 10000
SERVICE_DESCRIPTION = 'fire insurance'
API_KEY = os.getenv('UNIQUE_SIGNALS_API_KEY')

PAYLOAD = {
    'api_key': API_KEY,
    'business_type': BUSINESS_TYPE,
    'service_description': SERVICE_DESCRIPTION,
    'lng': LNG,
    'lat': LAT,
    'radius_m': RADIUS_M
}


def load_signals(payload: dict):
    """
    Function loads signals and businesses from unique signals API.

    :return:
    :rtype:
    """
    status = requests.post(
        url='https://uniquesignals.io/api/get-signals',
        headers={
            "Content-Type": "application/json"
        },
        json=payload
    )

    data = status.json()

    return data


ds = load_signals(PAYLOAD)

try:
    job_id = ds['job_id']
    PAYLOAD['job_id'] = job_id
except KeyError as ke:
    print("No job_id found, process is terminated")
    raise ke


counter = 0
while True:
    ds = load_signals(PAYLOAD)

    if ds['job_status'] == 'completed':
        break
    elif ds['job_status'] == 'error':
        raise RuntimeError(ds['error_message'])
    else:
        if counter == 12:
            raise TimeoutError('Cannot establish connection to UniqueSignals API')
        counter += 1
        time.sleep(10)

print('Downloading data')
data_url = ds['url']

with open('output.json', 'wb') as out_file:
    content = requests.get(data_url, stream=True).content
    out_file.write(content)

```

## Request structure

### URL

https://uniquesignals.io/api/

### Payload parameters

#### First invocation - you don't have `job_id` yet

- `api_key`
	- REQUIRED
	- **string**
	- API Key generated through uniquesignals.ai admin panel
- `lng`
	- REQUIRED
	- **numerical**
	- longitude where to search for the customers
- `lat`
	- REQUIRED
	- **numerical**
	- latitude where to search for the customers
- `radius_m`
	- REQUIRED
	- **numerical**
	- radius within the search is performed in meters, maximum radius is 10 kilometers (10 000 meters)
- `business_type`
	- OPTIONAL, must be provided along with `service_description`, when not provided you must use `icp_description` instead
	- **string**
	- description of your business, should be short: insurance broker, construction company, retailer, brewery
- `service_description`
	- OPTIONAL, must be provided along with `business_type`, when not provided you must use `icp_description` instead
	- **string**
	- description of your services, could be longer and specific: fire insurance for restaurants, design and construction of warehouses and data centers, small shop with fresh vegetables, non-alcoholic beers and wines
- `icp_description`
	- OPTIONAL, when not provided you must use `business_type` and `service_description` instead
	- **string**
	- description of your ICP (Ideal Customer Profile) or just the customer type, for example: restaurants, data center and computing services, chefs, event agencies

When you send this request you will get the response with job status, and there is one key: `job_id` that is used in the next invocations to retrieve data.

#### Second and next invocations - you have `job_id`

- `api_key`
	- REQUIRED
	- **string**
	- API Key generated through uniquesignals.ai admin panel
- `job_id`
	- REQUIRED
	- **string**
	- Unique job id that you get in the reponse after the first invocation

### Requests flow

1. Send request with parameters described in the first invocation.
2. You will get the response with `job_id` parameter.
3. Use `job_id` to send next requests to check if data is ready (to the same endpoint).

## Response structure

### API response

