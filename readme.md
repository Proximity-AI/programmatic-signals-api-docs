# UniqueSignals - programmatic access API docs

Last update: 2026-02-03

## Welcome

Welcome to documentation of `uniquesignals.io` API.

## Plans: free, PRO, Enterprise

| Feature | Free | Pro | Enterprise |
|---------|------|-----|------------|
| Access to `uniquesignals.ai` web service | yes | yes | yes |
| Access to API | no | yes | yes |
| Number of API keys per organization | 0 | 1 | custom |
| API requests per month | 0 | 100 | custom |
| E-mail enrichment | yes - limited to the web service | yes | yes |
| Number of signals | 1 signal type / POI | 1 signal type / POI | 1 or more signal types / POI |
| People API - know who is related to the business | no | yes | yes |

Enterprise partnership is limited to the selected organizations, and ends with separate contract agreement and API access endpoints. Would you like to know more? Please contact us: [here](billing@useproximity.ai)

## Comparison to Google Places

| Feature | Google Places | Unique Signals |
|---------|---------------|----------------|
| List of businesses | yes | yes |
| People related to the business | no | yes |
| Businesses unavailable on Google Maps | no | yes |
| Query using text - you know the ICP | yes | yes |
| Query based on your business category and your services - you don't know who your customer might be | no | yes |
| Basic address data | yes | yes |
| Emails | rare | yes - you can enrich data with emails (\*) |
| Neighborhood signals that might be used for the cold outreach | no | yes |
| Reasoning why does a signal is relevant for your potential customer | no | yes |

- (\*) - email enrichment is provided by default when querying `get-people` API endpoint

## Access to `uniquesignals` API

### PRO

To get access to the programmatic API od the `uniquesignals` system you must:

1. Register your organization on [Unique Signals Portal](https://uniquesignals.ai/auth/signup)
2. Upgrade to the [PRO Plan](https://uniquesignals.ai/pricing)
3. Request API Key:
  - Go into your [Admin Panel](https://uniquesignals.ai/admin)
  - Select API Keys
  - Create `New API Key` and copy it - it won't be visible later!
4. Start using the system!

### Enterprise

Please, contact our [support](billing@useproximity.ai)

## Example API usage

### Get signals and businesses

#### Using CURL

##### 1. Initiate processing

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

##### 2. Get link to the data

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

##### 3. Download data

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

#### Using Python

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

### Get people

#### Using CURL

##### 1. Initiate processing

```bash
curl -X POST "https://uniquesignals.io/api/get-people" \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your-api-key-generated-with-uniquesignals",
    "prompt": "Leisure activities and services around those in Miami, Florida"
  }'
```

You will get the following response:

```json
{
  "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97",
  "job_status": "running",
  "organization_id": "050e35f6-",
  "error_message": None,
  "started_at": "2026-03-13T10:11:17.666026+00:00",
  "completed_at": none,
  "url": none,
  "estimated_processing_time_s": 30,
  "n_records": 0,
  "n_emails": 0,
  "n_people": 0,
  "total_token_count": 0
}


```

##### 2. Get link to the data

After around 30 seconds you can query again the same endpoint, but this time using returned `job_id` parameter. If job is `completed` you will get a link valid for 60 seconds from where you can initiate download:

```bash
curl -X POST "https://uniquesignals.io/api/get-signals" \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your-api-key-generated-with-uniquesignals",
    "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97"
  }'
```

And then you get the final response with the link:

```json
{
  "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97",
  "job_status": "completed",
  "organization_id": "050e35f6-",
  "error_message": none,
  "started_at": "2026-03-13T10:11:17.666026+00:00",
  "completed_at": "2026-03-13T10:12:13.680778+00:00",
  "url": "https://.../b758371c-32af-4f4d-b477-758fb40e8f97.json?AWSAccessKeyId=...",
  "estimated_processing_time_s": 50,
  "n_records": 80,
  "n_emails": 12,
  "n_people": 121,
  "total_token_count": 30000
}

```

##### 3. Download data

Use this link to download the data:

```bash
wget "https://.../b758371c-32af-4f4d-b477-758fb40e8f97.json?AWSAccessKeyId=..."
```

```json
{
  "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97",
  "leads": [
    {
      "address": "1331 Brickell Bay Dr Miami 33131-3610, FL, US",
      "name": "StepFlix Entertainment",
      "business_category": "arts_and_entertainment",
      "operating_status": "open",
      "other_addresses": [],
      "other_phones": [],
      "other_websites": [],
      "phone": "+18564773549",
      "website": "https://www.stepflixentertainment.com/",
      "emails": [],
      "people": [
        {
          "id": "672773e8-ba24-4ded-a666-445097ed9978",
          "first_name": "Emily",
          "last_name": "Yero",
          "phone": null,
          "attributes": {
            "title": "CEO",
            "total_score": 0.545
          }
        }
      ]
    },
  ]
}
```

#### Using Python

**Note**: soon `uniquesignals.io` will have dedicated Python client!

When using Python it is easier to automate data retrieval. Below you have a full script that is using `requests` package and `uniquesignals.io` api.

```python
import os
import time

import requests


PROMPT = 'Leisure activities and services around those in Miami, Florida'
API_KEY = os.getenv('UNIQUE_SIGNALS_API_KEY')

PAYLOAD = {
    'api_key': API_KEY,
    'prompt': PROMPT
}


def load_people(payload: dict):
    """
    Function loads people and businesses from unique signals API.

    :return:
    :rtype:
    """
    status = requests.post(
        url='https://uniquesignals.io/api/get-people',
        headers={
            "Content-Type": "application/json"
        },
        json=payload
    )

    data = status.json()

    return data


ds = load_people(PAYLOAD)

try:
    job_id = ds['job_id']
    PAYLOAD['job_id'] = job_id
except KeyError as ke:
    print("No job_id found, process is terminated")
    raise ke


counter = 0
while True:
    ds = load_people(PAYLOAD)

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

##### Example

First invocation (job initiation).

```json
{
  "api_key": "your-api-key-generated-with-uniquesignals",
  "business_type": "insurance",
  "service_description": "fire insurance",
  "lng": -122.4194,
  "lat": 37.7749,
  "radius_m": 10000
}
```


#### Second and next invocations - you have `job_id`

- `api_key`
	- REQUIRED
	- **string**
	- API Key generated through uniquesignals.ai admin panel
- `job_id`
	- REQUIRED
	- **string**
	- Unique job id that you get in the reponse after the first invocation

##### Example

Next invocations (getting data).

```json
{
  "api_key": "your-api-key-generated-with-uniquesignals",
  "job_id": "b93b1d73-ee50-43db-b0e3-b57d00c820f4"
}
```

### Requests flow

1. Send request with parameters described in the first invocation.
2. You will get the response with `job_id` parameter.
3. Use `job_id` to send next requests to check if data is ready (to the same endpoint).

## Response structure

### API response

- `job_id`: **string** - unique identifier of the process, it is required to retrieve data, you must use it in the 2nd and next requests
- `organization_id`: **string** - your organization's unique identifier, you might use it for technical and operational support along with the `job_id`
`job_status`: **string** - could be: completed, pending, running, error, test, unknown. After the first request `job_status` will be `running`, and when your data is ready then `job_status` will be `completed`. Any other status means that something might be wrong with the request.
- `started_at`: **string** - timestamp in UTC time zone, when job has started
- `completed_at`: **string** - timestamp in UTC, when job has ended (data is ready), if it is `none` and `job_status` is `running` then your data is processed
- `error_message`: **string** - when `job_status` is `error` then this message points out why there is an error
- `url`: **string** - after job is completed you will get a url valid for 60 seconds from where you can download preapred JSON with data
- `estimated_processing_time`: EXPERIMENTAL **int** - time in seconds when data will be ready, right now it is experimental feature, and 30 seconds is close to the worst case scenario
- `n_records`: **int** - number of retrieved businesses
- `n_signals`: **int** - number of retrieved signals
- `total_token_count`: **int** - number of used tokens

#### Example - job initiation

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

#### Example - getting data

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

## Output data structure

### Type

- JSON

### Fields

- `job_id`: **string** - unique identifier of the process, the same that you get through the API respones
- `recommendation_score`: **int** - final recommendation score, within limits [1-10]
- `businesses_no_signals`: **list[string]** - list of business identifiers (keys) without any signals
- `businesses`: **dict[string]**
	- `biz_{i}`: **string** - i-th business, used as the unique ID to link businesses to signals
		- `address`: **string** - i-th business address
		- `name`: **string** - i-th business name
		- `website`: **string** - i-th business website URL
		- `phone`: **string** - i-th business phone number (local for each country)
		- `email`: **string** - i-th business email, not always provided
		- `uq_reasoning`: **string** - why this customer's bsuiness category might be important for your sales representative
		- `signal_ids`: **list[string]** - array with unique ids of signals related to the business
- `signals`: **dict[string]**
	- `{signal id}`: **string** - unique ID of a signal
		- `signal_type`: **string** - classification of the signal
		- `address`: **string** - when signal can be pointed to the specific address then it is provided
		- `city`: **string**
		- `state`: **string**
		- `description`: **string** - human-readable description of the signal
		- `event_time`: **string** - local time in the format *YYYY-MM-DDTHH:MM:SS.Z*
		- `reasoning`: **string** - why signal impacts given businesses from the `business_ids` list
		- `signal_metadata`: **dict** - additional metadata, different for different signal types
			- `signal_class`: **string** - a common field in Signal Metadata objects that describes Proximity signal class (hardcoded into the data models)
			- *other fields*, depending on the signal class
		- `business_ids`: **list[string]** - unique ids of businesses related to the signal

### Example

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

## Common issues

### No Places Error

- `error_message`: *No POIs found for the offered services! Set the bigger radius, or change the location.*
- solution: change the `lat`, `lng` location - probably there are no businesses of a concrete type in the area; sometimes you may increase the `radius_m` parameter. Do not go below 1000 meters. You may always use the biggest possible radius (10000 meters).
