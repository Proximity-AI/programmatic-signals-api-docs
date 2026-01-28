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
curl -X POST "https://api.uniquesignals.ai/get-signals" \
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
curl -X POST "https://api.uniquesignals.ai/get-signals" \
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

