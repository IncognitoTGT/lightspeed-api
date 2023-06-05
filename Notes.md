# Notes

## This is where I document our progress

### 6/3/23 - Day 1

After a lot of attempts of getting the extension to run on my Mac, I figured it out! I followed this:

1. Use the `ExtensionInstallAllowlist` policy to whitelist Lightspeed.

2. Grab the crx using the magic method, and install by dragging into chrome://extensions.

Profit!

Now, to inspect it, open DevTools, go to the Network tab, and set the user agent to ChromeOS. Disable the extension, and as soon as you reenable it, open DevTools.

Yessss I see something!
![](./Images//Day%201/Day%201.jpg)

Hmmm I wonder what's in the schema request. It seems to be sent by doorman.js, which I don't know the purpose of. Looking deeper, it seems to send the following to `https://reporting-api.relay.school/schema`. Going to the URL gives a 403 error. Back in DevTools, clicking the schema request gives this goldmine: 

![](/Images/Day%201/r1.jpg)

Well, this is interesting. It seems like the extension sends a request to an API. Seems like a way to ask if something's online. Judging by the Apigw-Requestid header, the server is hosted on AWS, like the rest of Lightspeed. The payload is a giant blob of text I'll put in the bottom of this file. Looking deeper, the response is the same. The way I'll tackle this is probably by returning the exact same request.



The user_policy request to `https://p7nvu5it0k.execute-api.us-west-2.amazonaws.com/production/filter/chrome/v2/user_policy` is interesting. This one seems to be the one that should be spoofed. This is since when I open it up, it sends a json telling the host info. The headers are below.

![](/Images/Day%201/r2.png)
 
 The payload json: 
 ```
{"directoryUsername":"Guest-1e1e4ba4-830f-40d3-a14d-ebda82436349","platform":"mac","udid":"Guest-1e1e4ba4-830f-40d3-a14d-ebda82436349","lastPolicyEpochMS":0,"agent_version":"3.4.0.1685041988"}
```
The response does tell what is blocked and unblocked. I'll paste it in the bottom.
Another thing to note is that each response is timestamped, which is something that I also have to do then. Something to point out is that the extension seems to be looking for updates every 5 minutes. 

There are also other requests that are interesting. I'll look into the others tommorrow since I'm tired. 









## Giant Text files:

#### The payload for the schema request:

```
type AuditLog {
  at_epoch_ms: String
  customer_id: String!
  reverse_host: String
  user_guid: String!
  video_id: String
}

type Filter {
  allow: String!
  at_epoch_ms: String!
  rg: String
  category_id: Int
  customer_id: String!
  device_sn: String
  dt: String
  group_guids: [String]
  host: String
  internal_ip: String
  is_asr: Boolean
  is_google_doc: Boolean
  os_type: String
  path: String
  protocol: String
  public_ip: String
  query: String
  reason: String
  request_type_id: Int
  reverse_host: String!
  search_query: String
  user_guid: String!
  username: String
  video_id: String
}

type Rollup {
  allowed_edu: Int
  allowed_non_edu: Int
  allows: Int
  blocks: Int
  customer_id: String!
  edu_time_on_page: Int
  other_time_on_page: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

type SearchTerms {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  search_term: String!
  user_guid: String!
}

type Time {
  category_id: Int
  customer_id: String!
  reverse_host: String!
  time_on_host: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

type TimeRollup {
  pk2: String
  sk2: String
  customer_id: String!
  reverse_host: String
  time_on_host: Int!
  user_guid: String!
  yyyy_week_number: String!
}

type Youtube {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  user_guid: String!
  video_id: String!
}

input TimeInput {
  category_id: Int
  customer_id: String!
  reverse_host: String!
  time_on_host: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input ChromeApp {
  id: String
  shortName: String
  installType: String
  isApp: Boolean
  mayDisable: Boolean
  version: String
}

input InstalledChromeApps {
  customer_id: String!
  userEmail: String!
  deviceSerial: String
  apps: [ChromeApp!]!
}
type result {
  cursor: String
  Items: [All]
}
input get_AuditLog {
  at_epoch_ms: String
  customer_id: String!
  reverse_host: String
  user_guid: String!
  video_id: String
}

input modify_AuditLog {
  at_epoch_ms: String
  customer_id: String!
  reverse_host: String
  user_guid: String!
  video_id: String
}

input put_AuditLog {
  at_epoch_ms: String
  customer_id: String!
  reverse_host: String
  user_guid: String!
  video_id: String
}

input batchPut_AuditLog {
  at_epoch_ms: String
  customer_id: String!
  reverse_host: String
  user_guid: String!
  video_id: String
}

input remove_AuditLog {
  at_epoch_ms: String
  customer_id: String!
  reverse_host: String
  user_guid: String!
  video_id: String
}

input get_Filter {
  allow: String!
  at_epoch_ms: String!
  rg: String
  category_id: Int
  customer_id: String!
  device_sn: String
  dt: String
  group_guids: [String]
  host: String
  internal_ip: String
  is_asr: Boolean
  is_google_doc: Boolean
  os_type: String
  path: String
  protocol: String
  public_ip: String
  query: String
  reason: String
  request_type_id: Int
  reverse_host: String!
  search_query: String
  user_guid: String!
  username: String
  video_id: String
}

input modify_Filter {
  allow: String!
  at_epoch_ms: String!
  rg: String
  category_id: Int
  customer_id: String!
  device_sn: String
  dt: String
  group_guids: [String]
  host: String
  internal_ip: String
  is_asr: Boolean
  is_google_doc: Boolean
  os_type: String
  path: String
  protocol: String
  public_ip: String
  query: String
  reason: String
  request_type_id: Int
  reverse_host: String!
  search_query: String
  user_guid: String!
  username: String
  video_id: String
}

input put_Filter {
  allow: String!
  at_epoch_ms: String!
  rg: String
  category_id: Int
  customer_id: String!
  device_sn: String
  dt: String
  group_guids: [String]
  host: String
  internal_ip: String
  is_asr: Boolean
  is_google_doc: Boolean
  os_type: String
  path: String
  protocol: String
  public_ip: String
  query: String
  reason: String
  request_type_id: Int
  reverse_host: String!
  search_query: String
  user_guid: String!
  username: String
  video_id: String
}

input batchPut_Filter {
  allow: String!
  at_epoch_ms: String!
  rg: String
  category_id: Int
  customer_id: String!
  device_sn: String
  dt: String
  group_guids: [String]
  host: String
  internal_ip: String
  is_asr: Boolean
  is_google_doc: Boolean
  os_type: String
  path: String
  protocol: String
  public_ip: String
  query: String
  reason: String
  request_type_id: Int
  reverse_host: String!
  search_query: String
  user_guid: String!
  username: String
  video_id: String
}

input remove_Filter {
  allow: String!
  at_epoch_ms: String!
  rg: String
  category_id: Int
  customer_id: String!
  device_sn: String
  dt: String
  group_guids: [String]
  host: String
  internal_ip: String
  is_asr: Boolean
  is_google_doc: Boolean
  os_type: String
  path: String
  protocol: String
  public_ip: String
  query: String
  reason: String
  request_type_id: Int
  reverse_host: String!
  search_query: String
  user_guid: String!
  username: String
  video_id: String
}

input get_Rollup {
  allowed_edu: Int
  allowed_non_edu: Int
  allows: Int
  blocks: Int
  customer_id: String!
  edu_time_on_page: Int
  other_time_on_page: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input modify_Rollup {
  allowed_edu: Int
  allowed_non_edu: Int
  allows: Int
  blocks: Int
  customer_id: String!
  edu_time_on_page: Int
  other_time_on_page: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input put_Rollup {
  allowed_edu: Int
  allowed_non_edu: Int
  allows: Int
  blocks: Int
  customer_id: String!
  edu_time_on_page: Int
  other_time_on_page: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input batchPut_Rollup {
  allowed_edu: Int
  allowed_non_edu: Int
  allows: Int
  blocks: Int
  customer_id: String!
  edu_time_on_page: Int
  other_time_on_page: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input remove_Rollup {
  allowed_edu: Int
  allowed_non_edu: Int
  allows: Int
  blocks: Int
  customer_id: String!
  edu_time_on_page: Int
  other_time_on_page: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input get_SearchTerms {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  search_term: String!
  user_guid: String!
}

input modify_SearchTerms {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  search_term: String!
  user_guid: String!
}

input put_SearchTerms {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  search_term: String!
  user_guid: String!
}

input batchPut_SearchTerms {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  search_term: String!
  user_guid: String!
}

input remove_SearchTerms {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  search_term: String!
  user_guid: String!
}

input batchPut_Time {
  category_id: Int
  customer_id: String!
  reverse_host: String!
  time_on_host: Int
  user_guid: String!
  yyyy_mm_dd: String!
}

input get_TimeRollup {
  pk2: String
  sk2: String
  customer_id: String!
  reverse_host: String
  time_on_host: Int!
  user_guid: String!
  yyyy_week_number: String!
}

input modify_TimeRollup {
  pk2: String
  sk2: String
  customer_id: String!
  reverse_host: String
  time_on_host: Int!
  user_guid: String!
  yyyy_week_number: String!
}

input put_TimeRollup {
  pk2: String
  sk2: String
  customer_id: String!
  reverse_host: String
  time_on_host: Int!
  user_guid: String!
  yyyy_week_number: String!
}

input batchPut_TimeRollup {
  pk2: String
  sk2: String
  customer_id: String!
  reverse_host: String
  time_on_host: Int!
  user_guid: String!
  yyyy_week_number: String!
}

input remove_TimeRollup {
  pk2: String
  sk2: String
  customer_id: String!
  reverse_host: String
  time_on_host: Int!
  user_guid: String!
  yyyy_week_number: String!
}

input get_Youtube {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  user_guid: String!
  video_id: String!
}

input modify_Youtube {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  user_guid: String!
  video_id: String!
}

input put_Youtube {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  user_guid: String!
  video_id: String!
}

input batchPut_Youtube {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  user_guid: String!
  video_id: String!
}

input remove_Youtube {
  at_epoch_ms: String!
  blocked: Boolean
  customer_id: String!
  reverse_host: String!
  user_guid: String!
  video_id: String!
}

union All = AuditLog | Filter | Rollup | SearchTerms | Time | TimeRollup | Youtube
type Query {
  get_AuditLog(item: get_AuditLog!): AuditLog
  get_Filter(item: get_Filter!): Filter
  get_Rollup(item: get_Rollup!): Rollup
  get_SearchTerms(item: get_SearchTerms!): SearchTerms
  get_Time(item: TimeInput!): Time
  get_TimeRollup(item: get_TimeRollup!): TimeRollup
  get_Youtube(item: get_Youtube!): Youtube
  query(queryParams: String, cursorString: String): result
  custom_chrome_apps(item: InstalledChromeApps!): Boolean
  custom_time_incriment(item: TimeInput!): Time
}
type Mutation {
  modify_AuditLog(item: modify_AuditLog!): AuditLog
  modify_Filter(item: modify_Filter!): Filter
  modify_Rollup(item: modify_Rollup!): Rollup
  modify_SearchTerms(item: modify_SearchTerms!): SearchTerms
  modify_Time(item: TimeInput!): Time
  modify_TimeRollup(item: modify_TimeRollup!): TimeRollup
  modify_Youtube(item: modify_Youtube!): Youtube
  put_AuditLog(item: put_AuditLog!): AuditLog
  put_Filter(item: put_Filter!): Filter
  put_Rollup(item: put_Rollup!): Rollup
  put_SearchTerms(item: put_SearchTerms!): SearchTerms
  put_Time(item: TimeInput!): Time
  put_TimeRollup(item: put_TimeRollup!): TimeRollup
  put_Youtube(item: put_Youtube!): Youtube
  batchPut_AuditLog(items: [batchPut_AuditLog!]!): Boolean
  batchPut_Filter(items: [batchPut_Filter!]!): Boolean
  batchPut_Rollup(items: [batchPut_Rollup!]!): Boolean
  batchPut_SearchTerms(items: [batchPut_SearchTerms!]!): Boolean
  batchPut_Time(items: [batchPut_Time!]!): Boolean
  batchPut_TimeRollup(items: [batchPut_TimeRollup!]!): Boolean
  batchPut_Youtube(items: [batchPut_Youtube!]!): Boolean
  remove_AuditLog(item: remove_AuditLog!): AuditLog
  remove_Filter(item: remove_Filter!): Filter
  remove_Rollup(item: remove_Rollup!): Rollup
  remove_SearchTerms(item: remove_SearchTerms!): SearchTerms
  remove_Time(item: TimeInput!): Time
  remove_TimeRollup(item: remove_TimeRollup!): TimeRollup
  remove_Youtube(item: remove_Youtube!): Youtube
}
schema {
  query: Query
  mutation: Mutation
}
```

#### The user policy payload
```
{
    "content_filter": {
        "categories": {
            "blocked": [0, 3, 4, 5, 8, 11, 12, 13, 19, 21, 28, 29, 31, 32, 33, 38, 39, 40, 42, 49, 55, 56, 60, 61, 66, 67, 69, 70, 71, 72, 78, 80, 81, 82, 93, 94, 98, 100, 101, 102, 105, 107, 113, 116, 117, 118, 126, 127, 133, 134, 135, 137, 139, 200, 201, 202, 203, 1003],
            "overrides": [],
            "lockouts": [],
            "readOnly": []
        },
        "bypass_on_failure": false,
        "chromebook_only": true,
        "lists": {
            "blocked": [{
                "host": "*unblockedgames*",
                "display": "*unblockedgames*",
                "flaggable": false
            }, {
                "host": "*miniproxy*",
                "display": "*miniproxy*",
                "flaggable": false
            }],
            "allowed": [{
                "host": "www.youtube-nocookie.com",
                "display": "www.youtube-nocookie.com",
                "flaggable": true
            }, {
                "host": "*.nettrekker.com",
                "display": "*.nettrekker.com",
                "flaggable": true
            }, {
                "host": "ssor.tribdss.com",
                "display": "ssor.tribdss.com",
                "flaggable": true
            }, {
                "host": "*tribdss.com*",
                "display": "*tribdss.com*",
                "flaggable": true
            }],
            "keywords": [],
            "lockout_notifications": []
        },
        "google_filter_thumbnails": true,
        "google_force_safesearch": true,
        "google_free_images": true,
        "ssl_exclusion_timestamp": "2023-05-25T14:06:38.131Z",
        "flag_mode": "page"
    },
    "videos": {
        "mode": {
            "blocked": false
        },
        "smart_play": {
            "block_other_videos": false,
            "smart_play": false
        },
        "youtube_restricted_mode": false,
        "youtube_lists": {
            "allowed_channel_ids": [],
            "blocked_channel_ids": [],
            "allowed_video_ids": [],
            "blocked_video_ids": []
        }
    },
    "flag_settings": {
        "docs.google.com": {
            "matcher": "docs.google.com",
            "mode": "page"
        }
    },
    "v": "2017-5-22",
    "can_override": false,
    "asr": {
        "mode": {
            "state": "off"
        }
    },
    "asr_policy": {
        "content_filter": {
            "categories": {
                "blocked": [4, 8, 11, 12, 19, 21, 28, 31, 32, 33, 38, 39, 42, 60, 61, 67, 70, 71, 72, 94, 100, 101, 102, 116, 117, 118, 126, 133, 134, 135, 137, 139, 200, 201, 202, 203],
                "overrides": [],
                "lockouts": [],
                "readOnly": []
            },
            "lists": {
                "blocked": [],
                "allowed": [],
                "keywords": [],
                "lockout_notifications": []
            },
            "flag_mode": "page"
        },
        "videos": {
            "mode": {
                "blocked": true,
                "video_mode": "filtered"
            },
            "youtube_lists": {
                "allowed_channel_ids": [],
                "blocked_channel_ids": [],
                "allowed_video_ids": [],
                "blocked_video_ids": []
            }
        }
    },
    "use_iot": false,
    "aa_tracking_location_active": false,
    "generated_at": 1685811016406,
    "flagged_terms": [],
    "reporting_interval_ms": 14000,
    "sm_enabled": false,
    "disable_service_filter": false,
    "time_zone": "America/New_York",
    "utc_offset": "-4",
    "hlm": "v1",
    "ylm": "v1",
    "bp_updated": "2023-05-12T14:17:12Z",
    "cc_updated": "2023-06-02T18:50:24Z",
    "cc_interval": 86400,
    "pl_interval": 300,
    "block_screen_body": null,
    "block_screen_color": "#FFBF0D",
    "block_screen_footer": null,
    "block_screen_name": null
}
```
### 6/5/23 - Day 2

So I can't examine the extension since it takes forever, and since im typing in a codespace :skull:. But,  I can examine the source code yay. Apperently, the p7* url for the user policy has backups, hence why it still worked even though I blocked it. Looking through the minified source code (AAAAAAAAAAAAAAAAAAAAAAAH), I see even more urls. It will be below. I'll probably need some trial and error to find the urls to spoof. There are some weird urls for things such as I-ready, wonder what they're for :thinking:

#### The urls

* lsrelay-config-production.s3.amazonaws.com,
* lsrelay-extensions-production.s3.amazonaws.com,
* p7nvu5it0k.execute-api.us-west-2.amazonaws.com,
* production-gc.lsfilter.com,
* lsrelayaccess.com,
* lsurl.me,
* lsmdm-production.s3.amazonaws.com,
* devices.lsclassroom.com,
* lsorchestration-production.amazonaws.com,
* lsaccess.me,
* www.googleapis.com,
* ajax.googleapis.com,
* googleapis.com,
* fonts.googleapis.com,
* login.i-ready.com,
* gm4nyg31l2.execute-api.us-west-2.amazonaws.com,
* 5rw61tcrl5.execute-api.us-west-2.amazonaws.com,
* wsmfcvajyf.execute-api.us-west-1.amazonaws.com,
* development.lsclassroom.com,
* staging-preview.lsclassroom.com,
* preview.lsclassroom.com,
* hosted186.renlearn.com,
* z40.renlearn.com,
* z40.renlearnrp.com,
* z46.renlearn.com,
* z46.renlearnrp.com,
* hosted298.renlearn.com,
* realtime.ably.io,
* z05.renlearn.com,
* z05.renlearnrp.com,
* hosted88.renlearn.com,
* gce-beacons.gcp.gvt2.com,
* gce-beacons.gcp.gvt3.com,
* rest.ably.io,
* lightspeed-realtime.ably.io,
* a-fallback-lightspeed.ably.io,
* b-fallback-lightspeed.ably.io,
* c-fallback-lightspeed.ably.io

