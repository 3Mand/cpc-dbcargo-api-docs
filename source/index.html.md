---
title: CPC DBCargo API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

search: false
---

# Introduction

Welcome to the DB Cargo API! This documentation covers data transmission between CPC and DB Cargo, regarding tracker locations, geofence alerts and daily digests.

We have language bindings in Shell! You can view code examples in the dark area to the right.

# Authentication on CPC endpoints

The CPC Endpoints are protected by an API key. This token is required with each request. CPC endpoints expects for the API key to be included in all API requests to the server in the query parameters.

The API key is private and must be stored server-side on DB Cargo systems. It must not be accessible to users (e.g. through the source code or by sniffing network traffic).

Alternative: We can implement OAuth2 if the end-users should be able to log into the app with their CPC user/password credentials.

# CPC Endpoints

## Trackers

```shell
curl "https://dbcargo.cpctracking.dk/api/external/trackers?api_key=YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
[
  {
      "id": 1,
      "lat": "55.43884667",
      "lng": "11.81668167",
      "name": "EG3101",
      "location_time": "1975-05-21 22:00:00",
      "speed": 12.2
  },
  {
      "id": 2,
      "lat": "55.43965167",
      "lng": "11.82323167",
      "name": "BR185-323",
      "location_time": "1975-05-21 22:00:00",
      "speed": 25.3
  }
]
```

This endpoint retrieves all trackers with location, name, speed, and a timestamp for the last position.

### Tracker object description

Parameter | Description
--------- | -----------
id | The unique Tracker ID of the tracker triggering the event
lat | The last known latitude of the tracker
lng | The last known longitude of the tracker
name | Name of the tracker triggering the event
location_time | A timestamp in UTC (in format 1975-05-21 22:00:00)
speed | The last known speed of the tracker

### HTTP Request

`GET https://dbcargo.cpctracking.dk/api/external/trackers?api_key=YOUR_TOKEN`







# DB Cargo API Endpoints

## Daily digest export

```shell

```

> Example for daily digest (Values are made up)

```json
{
    "trackers": [
      {
          "id": 1,
          "name": "EG3101",
          "tripmeter": 1122651,
          "days_since_last_service": 12,
          "km_since_last_service": 3520,
          "timestamp": "1975-05-21 22:00:00"
      },
      {
          "id": 2,
          "name": "BR185-323",
          "tripmeter": 293191,
          "days_since_last_service": 9,
          "km_since_last_service": 2011,
          "timestamp": "1975-05-21 22:00:00"
      }
    ]
}
```

> The command returns JSON structured like this:

```json
{
  "status": "ok",
}
```

This endpoint receives a list of all trackers with name, tripmeter, days and distance since last service.

<aside class="notice">This API endpoint is on DB Cargo systems</aside>

### HTTP Request

`POST http://dbcargo.com/api/cpc/daily_digest`

### Query Parameters

Parameter | Description
--------- | -----------
trackers | Array of trackers with the format descibed below

### Tracker object description

Parameter | Description
--------- | -----------
id | The unique Tracker ID of the tracker triggering the event
name | Name of the tracker triggering the event
tripmeter | Current tripmeter of the tracker
days_since_last_service | Days since last service
km_since_last_service | Distance in km since last service
timestamp | A timestamp in UTC (in format 1975-05-21 22:00:00)

## Receive geofence notifications

```shell

```

> Example for a tracker arriving at a workshop in Kolding (Values are made up)

```json
{
    "geofence_id": 10,
    "geofence_name": "Workshop Kolding",
    "geofence_event_id": 8,
    "geofence_event_condition": "arrive",
    "geofence_event_message": "Arriving at Workshop Kolding",
    "tracker_id": 2,
    "tracker_name": "BR185-323",
    "tracker_tripmeter": 193028,
    "timestamp": "1975-05-21 22:00:00",
}
```
> Example for a tracker leaving a workshop in Kolding (Values are made up)

```json
{
    "geofence_id": 10,
    "geofence_name": "Workshop Kolding",
    "geofence_event_id": 9,
    "geofence_event_condition": "leave",
    "geofence_event_message": "Leaving Workshop Kolding",
    "tracker_id": 2,
    "tracker_name": "BR185-323",
    "tracker_tripmeter": 193030,
    "timestamp": "1975-05-21 22:00:00",
}
```
> Example for a tracker passing the danish border from Denmark towards Germany (Values are made up)

```json
{
    "geofence_id": 11,
    "geofence_name": "Danish/German border",
    "geofence_event_id": 10,
    "geofence_event_condition": "leave-s",
    "geofence_event_message": "Leaving Denmark",
    "tracker_id": 2,
    "tracker_name": "BR185-323",
    "tracker_tripmeter": 193990,
    "timestamp": "1975-05-21 22:00:00",
}
```

> The endpoint returns JSON structured like this:

```json
{
  "status": "ok",
}
```

This endpoint is triggered on specific geofence notifications.

<aside class="notice">This API endpoint is on DB Cargo systems</aside>

### HTTP Request

`POST http://dbcargo.com/api/cpc/geofence_event`

### Query Parameters

Parameter | Description
--------- | -----------
geofence_id | The unique ID of the geofence
geofence_name | The name of the geofence
geofence_event_id | The unique ID of the geofence event.
geofence_event_condition | One of "arrive" or "leave", can be appended with the direction of arrival (one of "-n", "-s", "-e", "-w") like so: "arrive-n", "arrive-e", "leave-s" etc.
geofence_event_message | A human readable text-string of the geofence event name.
tracker_id | The unique Tracker ID of the tracker triggering the event
tracker_name | Name of the tracker triggering the event
tracker_tripmeter | Current tripmeter of the tracker
timestamp | A timestamp in UTC (in format 1975-05-21 22:00:00)

