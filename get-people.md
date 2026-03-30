# API docs

## Disclaimer

The API endpoints and code examples presented here are provided for informational purposes only and are intended to illustrate potential integration patterns. The `people` and `emails` data are not currently available for commercial use. Access to this data is exclusively available through our invite-only test partnership program, and only for the limited area. If you are interested in early access, please contact us to learn more about partnership opportunities.

## `get-people`

Endpoint allowing you to get list of your ICP, and contacts linked to those businesses.

You might query it using prompt-like structure or fixed values.

## Payload Model

```yaml
components:
  schemas:
    GetPeoplePayload:
      type: object
      required:
        - api_key
      properties:
        api_key:
          type: string
          description: Your API authentication key
        job_id:
          type: string
          nullable: true
          description: Optional job identifier to retrieve results of a previous request
        prompt:
          type: string
          nullable: true
          description: Natural language prompt to filter or describe target companies
        icp:
          type: string
          nullable: true
          description: Ideal Customer Profile identifier for targeting
        lng:
          type: number
          format: float
          nullable: true
          description: Longitude coordinate for geospatial filtering
        lat:
          type: number
          format: float
          nullable: true
          description: Latitude coordinate for geospatial filtering
        radius_m:
          type: number
          format: float
          nullable: true
          description: Search radius in meters around the provided coordinates

```

Fields:

- `api_key`: required, your unique API Key
- `job_id`: optional for initialization, required for data loading (you query the same endpoint)
- `prompt`: optional, but you must provide it if `icp, lng, lat, radius_m` fields are empty. Valid prompt should define you ICP and area where you are searching, example: *Restaurants in Miami, Fl*
- `icp`: optional, but must be provided if `prompt` is empty. Your ICP description, examples: *restaurants*, *nail salon*, *financial services*
- `lng`: optional, but must be provided if `prompt` is empty. The exact longitude where search is performed in decimal degrees - like in GPS readings, it could be `-80.198733`
- `lat`: optional, but must be provided if `prompt` is empty. The exact latitude where search is performed in decimal degrees - like in GPS readings, it could be `25.800425`
- `radius_m` - optional, but must be provided if `prompt` is empty. The search radius in meters from `lat, lng` pair to find your clients, maximum radius is 10000 meters (10 kilometers => 6.2 miles).

You always call this endpoint, first - to initialize data retrieval, and second - to get prepared data. (See **Calling endpoint** section to learn more).

## Response Model

```yaml
components:
  schemas:
    GetPeopleResponse:
      type: object
      properties:
        job_id:
          type: string
          format: uuid
          description: Unique identifier for the job
          example: b758371c-32af-4f4d-b477-758fb40e8f97
        job_status:
          type: string
          enum: [pending, running, completed, failed]
          description: Current status of the job
          example: running
        organization_id:
          type: string
          description: Identifier of the organization that owns the job
          example: 050e35f6-
        error_message:
          type: string
          nullable: true
          description: Error details if the job failed, null otherwise
        started_at:
          type: string
          format: date-time
          nullable: true
          description: ISO 8601 timestamp of when the job started
          example: 2026-03-13T10:11:17.666026+00:00
        completed_at:
          type: string
          format: date-time
          nullable: true
          description: ISO 8601 timestamp of when the job completed, null if still running
        url:
          type: string
          format: uri
          nullable: true
          description: Download URL for the result data once the job is completed
        n_records:
          type: integer
          description: Total number of records found
          example: 0
        n_emails:
          type: integer
          description: Total number of emails in records found
          example: 0
        n_people:
          type: integer
          description: Total number of people in records found
          example: 0
        data:
          nullable: true
          description: Inline result payload when available, null if data is served via URL
          oneOf:
            - type: array
              items:
                type: object
            - type: object
```

### Data output

```yaml
components:
  schemas:

    OutputPeopleModel:
      type: object
      required:
        - job_id
        - leads
      properties:
        job_id:
          type: string
          format: uuid
          description: Unique identifier for the job
          example: b758371c-32af-4f4d-b477-758fb40e8f97
        leads:
          type: array
          description: List of matched businesses with enriched data
          items:
            $ref: '#/components/schemas/BusinessBaseModel'

    BusinessBaseModel:
      type: object
      required:
        - address
        - name
      properties:
        address:
          type: string
          description: Primary address of the business
        name:
          type: string
          description: Business name
        phone:
          type: string
          nullable: true
          description: Primary phone number
        website:
          type: string
          format: uri
          nullable: true
          description: Primary website URL
        business_category:
          type: string
          nullable: true
          description: Category or industry classification of the business
        operating_status:
          type: string
          nullable: true
          description: Current operating status of the business
          example: operational
        other_addresses:
          type: array
          nullable: true
          description: Additional known addresses
          items:
            type: string
        other_phones:
          type: array
          nullable: true
          description: Additional known phone numbers
          items:
            type: string
        other_websites:
          type: array
          nullable: true
          description: Additional known website URLs
          items:
            type: string
            format: uri
        emails:
          type: array
          nullable: true
          description: List of associated email addresses
          items:
            $ref: '#/components/schemas/EmailBaseModel'
        people:
          type: array
          nullable: true
          description: List of associated people / contacts
          items:
            $ref: '#/components/schemas/PeopleBaseModel'

    EmailBaseModel:
      type: object
      required:
        - address
      properties:
        address:
          type: string
          format: email
          description: Email address
          example: contact@example.com
        is_primary:
          type: boolean
          nullable: true
          default: true
          description: Whether this is the primary email address for the business
        created_at:
          type: string
          format: date-time
          nullable: true
          description: ISO 8601 timestamp of when the email record was created
        updated_at:
          type: string
          format: date-time
          nullable: true
          description: ISO 8601 timestamp of when the email record was last updated

    PeopleBaseModel:
      type: object
      required:
        - first_name
        - last_name
      properties:
        first_name:
          type: string
          description: Person's first name
          example: Jane
        last_name:
          type: string
          description: Person's last name
          example: Doe
        created_at:
          type: string
          format: date-time
          nullable: true
          description: ISO 8601 timestamp of when the person record was created
        updated_at:
          type: string
          format: date-time
          nullable: true
          description: ISO 8601 timestamp of when the person record was last updated
        phone:
          type: string
          nullable: true
          description: Person's phone number
          example: +1-555-123-4567
        email:
          type: string
          format: email
          nullable: true
          description: Person's email address
          example: jane.doe@example.com
        title:
          type: string
          nullable: true
          description: Person's job title or role
          example: Chief Financial Officer
        match_strength:
          nullable: true
          description: Confidence level of the ICP match
          $ref: '#/components/schemas/MatchStrength'

    MatchStrength:
      type: string
      enum:
        - strong
        - medium
        - weak
      description: Confidence level indicating how strongly a person matches the target ICP
      example: strong

```
          

## Calling the endpoint using prompt

### Using CURL

#### 1. Initiate processing

```bash
curl -X POST "https://uniquesignals.io/api/get-people" \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your-api-key-generated-with-uniquesignals",
    "prompt": "Leisure activities and services in Miami, Florida"
  }'
```

You will get the following response:

```json
{
  "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97",
  "job_status": "running",
  "organization_id": "050e35f6-",
  "error_message": none,
  "started_at": "2026-03-13T10:11:17.666026+00:00",
  "completed_at": none,
  "url": none,
  "n_records": 0,
  "n_emails": 0,
  "n_people": 0,
  "data": none
}

```

#### 2. Get data directly in the response

After around 30 seconds you can query again the same endpoint, but this time using returned `job_id` parameter. If job is `completed` and you are on PRO tier then responses are limited to 30 records, and data is returned directly in a response body under `data` field. The example:

```
{
  "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97",
  "job_status": "completed",
  "organization_id": "050e35f6-",
  "error_message": none,
  "started_at": "2026-03-13T10:11:17.666026+00:00",
  "completed_at": "2026-03-13T10:12:13.680778+00:00",
  "url": "https://.../b758371c-32af-4f4d-b477-758fb40e8f97.json?AWSAccessKeyId=...",
  "n_records": 30,
  "n_emails": 12,
  "n_people": 121,
  "data": {
    "job_id": "b758371c-32af-4f4d-b477-758fb40e8f97",
    "leads": [
      {},
      {
	     "address": "801 S Miami Ave, Miami, FL 33130, USA",
	     "name": "Brickell Watersports Club",
	     "phone": "+1-305-555-0142",
	     "website": "https://www.brickellwatersports.com",
	     "business_category": "Watersports & Outdoor Recreation",
	     "operating_status": "operational",
	     "other_addresses": [
	       "3301 Rickenbacker Causeway, Miami, FL 33149, USA"
	     ],
	     "other_phones": [
	       "+1-305-555-0199"
	     ],
	     "other_websites": [
	       "https://book.brickellwatersports.com"
	     ],
	     "emails": [
	       {
	         "address": "hello@brickellwatersports.com",
	         "is_primary": true,
	         "created_at": "2024-03-10T08:00:00+00:00",
	         "updated_at": "2025-01-20T11:00:00+00:00"
	       },
	       {
	         "address": "bookings@brickellwatersports.com",
	         "is_primary": false,
	         "created_at": "2024-03-10T08:00:00+00:00",
	         "updated_at": "2025-01-20T11:00:00+00:00"
	       }
	     ],
	     "people": [
	       {
	         "first_name": "Carlos",
	         "last_name": "Menendez",
	         "created_at": "2024-03-10T08:00:00+00:00",
	         "updated_at": "2025-02-01T09:00:00+00:00",
	         "phone": "+1-305-555-0155",
	         "email": "c.menendez@brickellwatersports.com",
	         "title": "Owner & Head Instructor",
	         "match_strength": "strong"
	       }
	     ],
	     "socials": [
	       "https://www.instagram.com/brickellwatersports",
	       "https://www.facebook.com/brickellwatersports"
	     ]
	   },
    ]
  }
}

```



#### 3. Get link to the data (big chunks)

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
  "n_people": 121
}

```

#### 4. Download data

Use this link to download the data:

```bash
wget "https://.../b758371c-32af-4f4d-b477-758fb40e8f97.json?AWSAccessKeyId=..."
```

```json

```

### Using Python

When using Python it is easier to automate data retrieval. Below you have a full script that is using `requests` package and `uniquesignals.io` api.

```python
import os
import time

import requests


PROMPT = 'Leisure activities and services in Miami, Florida'
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
