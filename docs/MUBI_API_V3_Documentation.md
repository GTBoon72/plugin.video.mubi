
# MUBI API V3 Documentation
## Disclaimer: This documentation was generated by ChatGPT.

### Introduction
This document provides technical documentation of the MUBI API V3. The API allows interaction with MUBI's services, including authentication, browsing films, collections, cast members, lists, events, and more.

The base URL for all API endpoints is:
```
https://api.mubi.com/v3/
```

### Table of Contents
1. [Authentication](#authentication)
   - [Get Link Code](#get-link-code)
   - [Authenticate](#authenticate)
   - [Logout](#logout)
2. [Films](#films)
   - [Browse Films](#browse-films)
   - [Get Film Viewing](#get-film-viewing)
   - [Get Secure Streaming URL](#get-secure-streaming-url)
   - [Get Playback Languages](#get-playback-languages)
3. [Prerolls](#prerolls)
   - [Create Preroll Viewing](#create-preroll-viewing)
4. [Search](#search)
   - [Search Content](#search-content)
5. [Collections](#collections)
   - [Browse Collections](#browse-collections)
   - [Get Collection Items](#get-collection-items)
6. [Cast Members](#cast-members)
   - [Browse Cast Members](#browse-cast-members)
   - [Get Cast Member Details](#get-cast-member-details)
   - [Get Films by Cast Member](#get-films-by-cast-member)
7. [Lists](#lists)
   - [Browse Lists](#browse-lists)
   - [Get List Films](#get-list-films)
8. [Industry Events](#industry-events)
   - [Browse Industry Events](#browse-industry-events)
   - [Get Event Years](#get-event-years)
   - [Get Event Films](#get-event-films)

### Authentication
#### Get Link Code

##### Endpoint

```bash
GET /v3/link_code
```

##### Description

Generates a link code and authentication token required for device authentication.

##### Headers

```
User-Agent: Custom user agent string
Additional headers for Android TV client identification.
```

##### Response

```json
{
  "auth_token": "string",
  "link_code": "string"
}
```

#### Authenticate

##### Endpoint

```bash
POST /v3/authenticate
```

##### Description

Authenticates the user using the auth_token obtained from the link code.

##### Headers

```
User-Agent: Custom user agent string
Additional headers for Android TV client identification.
```

##### Request Body

```json
{
  "auth_token": "string"
}
```

##### Response

```json
{
  "token": "string",
  "user": { /* User details */ }
}
```



#### Logout

##### Endpoint

```bash
DELETE /v3/sessions
```

##### Description

Logs out the authenticated user and invalidates the session token.

##### Headers

```
Authorization: Bearer {token}
Additional headers for Android TV client identification.
```

##### Response

```
HTTP Status 200 on success.
```


### Films

#### Browse Films

##### Endpoint

```bash
GET /v3/browse/films
```

##### Description

Retrieves a list of films based on sorting and filtering parameters.

##### Headers

```
Standard headers for web client identification.
```

##### Query Parameters

```
sort: Sorting option (e.g., "popularity")
playable: true (to retrieve only playable films)
Additional filters as needed (e.g., genre, country)
page: Page number for pagination
```

##### Response

```json
{
  "films": [ /* Array of film objects */ ],
  "meta": {
    "next_page": "integer or null",
    /* Other metadata */
  }
}
```



#### Get Film Viewing

##### Endpoint

```bash
POST /v3/films/{id}/viewing?parental_lock_enabled=true
```

##### Description

Initiates a viewing session for a specific film.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Path Parameters

```
id: Film ID
```

##### Request Body

```
Empty JSON object {}
```

##### Response

```
Details about the viewing session or error message.
```




#### Get Secure Streaming URL

##### Endpoint

```http
GET /v3/films/{film_id}/viewing/secure_url
```

##### Description

Retrieves a secure streaming URL and associated metadata for the specified film. This endpoint provides all the necessary information to stream the film, including available stream URLs (DASH, HLS, Smooth Streaming), DRM license details, subtitles, and other relevant data required for playback.

##### Authentication

This endpoint requires authentication. The Authorization header must include a valid bearer token obtained after successful authentication.

##### Request Headers

```http
Authorization: Bear {access_token}
User-Agent: {user_agent}
Client: {client_identifier}
Anonymous_user_id: {anonymous_user_id}
Accept-Encoding: gzip
Accept: application/json
Referer: https://mubi.com
Origin: https://mubi.com
Client-Accept-Audio-Codecs: aac
Client-Accept-Video-Codecs: h265,vp9,h264
```

- Authorization: Bearer token for authentication.
- User-Agent: Identifies the client software (e.g., web browser or app).
- Client: Identifies the type of client (e.g., "web").
- Anonymous_user_id: A unique identifier for anonymous users.
- Client-Accept-Audio-Codecs: Specifies accepted audio codecs.
- Client-Accept-Video-Codecs: Specifies accepted video codecs.

##### Path Parameters

- **film_id** (string): The unique identifier of the film for which to retrieve the secure streaming URL.

##### Request Example

```http
GET /v3/films/337/viewing/secure_url HTTP/1.1
Host: api.mubi.com
Authorization: Bearer ad1f756ffc44017bebf1b745ee18eeee81f100
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:125.0) Gecko/20100101 Firefox/125.0
Client: web
Anonymous_user_id: d0203e61-6809-6755-0ff6-f1a513bfa217
Accept-Encoding: gzip
Accept: application/json
Referer: https://mubi.com
Origin: https://mubi.com
Client-Accept-Audio-Codecs: aac
Client-Accept-Video-Codecs: h265,vp9,h264
```

##### Response
The response is a JSON object containing the secure streaming URL and associated metadata necessary for playback, including:

- Streaming URLs for different protocols (DASH, HLS, Smooth Streaming).
- DRM information for content protection.
- Available subtitle tracks in various languages.
- Video codec and stream properties.
- Metadata for analytics or tracking.

##### Response Example

```json
{
  "url": "https://germany-edge3.mubicdn.net/stream/dd4097b6af87fb49c595cf9410795a34/6709afa4/cf6c4d9b/mubi-films/337/happy-together_zho_zxx_3840x2160_69007_mezz35072/7565fbdf53/drm_playlist.aca5788fa1.ism/default/ver1.AVC1.2160p.mpd",
  "url_ttl": "2024-10-11T22:52:16Z",
  "signature": "dd4097b6af87fb49c595cf9410795a34/6709afa4/cf6c4d9b",
  "fallback_urls": [],
  "urls": [
    {
      "src": "https://germany-edge3.mubicdn.net/stream/.../drm_playlist.aca5788fa1.ism/default/ver1.AVC1.2160p.idx-3.m3u8",
      "content_type": "application/x-mpegURL"
    },
    {
      "src": "https://germany-edge3.mubicdn.net/stream/.../drm_playlist.aca5788fa1.ism/default/ver1.AVC1.2160p.mpd",
      "content_type": "application/dash+xml"
    },
    {
      "src": "https://germany-edge3.mubicdn.net/stream/.../drm_playlist.aca5788fa1.ism/Manifest?max_height=2160",
      "content_type": "application/vnd.ms-sstr+xml"
    }
  ],
  "text_track_urls": [
    {
      "id": "text_subtitle_en",
      "url": "https://germany-edge3.mubicdn.net/stream/.../eng.aca5788fa1.vtt",
      "url_ttml": "https://germany-edge3.mubicdn.net/stream/.../eng.aca5788fa1.ttml",
      "role": "subtitle",
      "language_code": "en"
    },
    {
      "id": "text_subtitle_it",
      "url": "https://germany-edge3.mubicdn.net/stream/.../ita.aca5788fa1.vtt",
      "url_ttml": "https://germany-edge3.mubicdn.net/stream/.../ita.aca5788fa1.ttml",
      "role": "subtitle",
      "language_code": "it"
    }
  ],
  "drm": {
    "asset_id": "337__happy-together_zho_zxx_3840x2160_69007_mezz35072__7565fbdf53__aca5788fa1",
    "variant_id": ""
  },
  "video_codec": "h264",
  "stream_start_index": 3,
  "mux": {
    "video_title": "Happy Together",
    "video_id": 337,
    "video_variant_id": "33973",
    "video_encoding_variant": "h264",
    "view_session_id": "dd4097b6af87fb49c595cf9410795a34/6709afa4/cf6c4d9b",
    "video_content_type": "movie",
    "video_stream_type": "on-demand",
    "custom_1": "movie",
    "custom_2": "2160",
    "custom_3": "UHD",
    "viewer_user_id": 8515840,
    "video_cdn": "germany-edge3.mubicdn.net"
  },
  "download_size": 0
}
```

##### Response Fields

- **url** (string): The primary secure streaming URL for the film, typically pointing to a DASH manifest (.mpd file).
- **url_ttl** (string): The expiration time (Time-To-Live) of the streaming URLs in ISO 8601 format. After this time, the URLs will no longer be valid.
- **signature** (string): A signature associated with the streaming session, possibly used for authentication or tracking purposes.
- **urls** (array): A list of available streaming URLs with different protocols and formats.
  - **src** (string): The streaming URL.
  - **content_type** (string): The MIME type indicating the streaming protocol and format.
    - application/x-mpegURL: HLS (HTTP Live Streaming).
    - application/dash+xml: DASH (Dynamic Adaptive Streaming over HTTP).
    - application/vnd.ms-sstr+xml: Microsoft Smooth Streaming.
- **text_track_urls** (array): A list of available subtitle tracks in various languages.
  - **id** (string): A unique identifier for the subtitle track.
  - **url** (string): The URL to the WebVTT (.vtt) subtitle file.
  - **url_ttml** (string): The URL to the TTML (.ttml) subtitle file.
  - **role** (string): The role of the text track, usually "subtitle".
  - **language_code** (string): The ISO 639-1 language code of the subtitle track (e.g., "en" for English).
- **drm** (object): Information related to Digital Rights Management (DRM).
  - **asset_id** (string): The unique identifier of the DRM-protected asset.
  - **variant_id** (string): An identifier for a specific variant of the asset, if applicable.
- **video_codec** (string): The video codec used for encoding, such as "h264".
- **stream_start_index** (integer): The index indicating the starting point of the stream in the manifest or playlist.
- **mux** (object): Metadata for analytics, tracking, or internal use.
  - video_title (string): The title of the video.
  - video_id (integer): The unique identifier of the video.
  - video_variant_id (string): Identifier for a specific variant of the video encoding.
  - video_encoding_variant (string): The encoding variant used, e.g., "h264".
  - view_session_id (string): Identifier for the current viewing session.
  - video_content_type (string): The type of content, e.g., "movie" or "trailer".
  - video_stream_type (string): The stream type, e.g., "on-demand".
  - custom_1, custom_2, custom_3 (string): Custom fields for additional metadata, such as resolution indicators ("2160" for 2160p UHD).
  - viewer_user_id (integer): The user ID of the viewer.
  - video_cdn (string): The Content Delivery Network (CDN) domain used for streaming.

Download Size
- download_size (integer): The size of the downloadable content in bytes, if applicable (usually 0 if downloading is not supported).

##### Important Notes

* URL Expiration: The url_ttl field indicates when the streaming URLs expire. Clients should ensure they use the URLs before this time and be prepared to request new ones if necessary.
* DRM Handling: Since the content is DRM-protected, clients must handle license acquisition and decryption according to the DRM system's requirements (e.g., Widevine). This usually involves sending a license request to a license server and handling the response appropriately.
* Streaming Protocols: Clients should select the streaming protocol best suited to the target device and playback capabilities. For example, DASH with Widevine DRM is commonly supported across a wide range of devices.
* Subtitles: Clients can offer users the ability to select subtitle languages based on the available tracks in the text_track_urls array.
* Analytics and Tracking: The mux object contains data that may need to be sent to analytics services to track playback performance and user engagement.

##### Error Handling

If the request fails, the API will return an appropriate HTTP status code along with an error message in the response body.
Example Error Response

```
{
  "error": "Unauthorized",
  "message": "Invalid or expired token."
}
```

* HTTP Status Code: Corresponds to the type of error (e.g., 401 Unauthorized for authentication errors).
* error: A brief error code or description.
* message: A detailed message explaining the error.

##### Usage Example

To play the film, a client application should:

1. Request the Secure URL: Use this endpoint to obtain the secure streaming URLs and metadata.
2. Select a Stream: Choose the appropriate streaming URL from the urls array based on supported protocols and DRM capabilities.
3. Handle DRM License Acquisition: Use the DRM information to acquire the necessary license for decryption.
4. Configure Subtitles: Retrieve the desired subtitle track(s) from text_track_urls and integrate them into the player.
5. Start Playback: Use the selected stream URL and DRM license to start playback in the media player.

##### Additional Considerations

* Adaptive Streaming: The provided URLs point to manifests or playlists that enable adaptive streaming. The media player should support adaptive bitrate streaming to switch between different quality levels based on network conditions.
* Device Compatibility: Ensure that the selected streaming protocol and DRM scheme are compatible with the target device and player software.
* Caching and Security: Do not cache the secure URLs or DRM licenses beyond their validity periods. Implement proper security measures to protect the content and user data.



#### Get Playback Languages

##### Endpoint

```bash
GET /v3/films/{id}/playback_languages
```

##### Description

Retrieves available audio and subtitle languages for a film.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Path Parameters

```
id: Film ID
```

##### Response

```json
{
  "audio_options": ["string"],
  "subtitle_options": ["string"],
  "media_features": ["string"]
}
```


### Prerolls

#### Create Preroll Viewing

##### Endpoint

```bash
POST /v3/prerolls/viewings
```

##### Description

Records a preroll viewing event for analytics or tracking.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Request Body

```json
{
  "viewing_film_id": integer
}
```

##### Response

```
HTTP Status indicating success or failure.
```


### Search

#### Search Content

##### Endpoint

```bash
GET /v3/search/{type}
```

##### Description

Searches for content of a specific type.

##### Headers

```
Standard headers for web client identification.
```

##### Path Parameters

```
type: Type of content to search (films, cast_members, etc.)
```

##### Query Parameters

```
query: Search query string
page: Page number for pagination
per_page: Number of results per page (default: 24)
playable: true (optional, to retrieve only playable content)
```

##### Response

```
Varies based on type, generally includes an array of results and metadata.
```


### Collections


#### Browse Collections

##### Endpoint
**GET /v3/browse/film_groups**

##### Description
Retrieves a list of film collections (also known as film groups) from MUBI. Each film group represents a curated collection of films, such as "Top 1000", "Film of the Day", or other thematic collections.

##### Headers
Include standard headers for client identification. If using the Android TV client, headers might look like:
```
User-Agent: MUBI-Android-TV/31.1
accept-encoding: gzip
accept: application/json
client: android_tv
client-version: 31.1
client-device-identifier: {device_id}
client-app: mubi
client-device-brand: unknown
client-device-model: sdk_google_atv_x86
client-device-os: 8.0.0
client-accept-audio-codecs: AAC
client-country: {client_country}
```

##### Query Parameters
- **sort** (optional): Sorting option for the film groups.
- **page** (optional): Page number for pagination (default is 1).

##### Response
```
{
  "film_groups": [
    {
      "id": integer,
      "slug": "string",
      "color": "string",
      "hide_title_on_splash": boolean,
      "double_bill": boolean,
      "full_title": "string",
      "full_title_upcase": "string",
      "title": "string",
      "title_upcase": "string",
      "title_color": "string",
      "subtitle": "string or null",
      "subtitle_upcase": "string or null",
      "subtitle_color": "string",
      "average_colour_hex": "string",
      "focal_point": {
        "x": float,
        "y": float
      },
      "image": "string",  // URL to the image
      "short_description": "string",
      "short_description_html": "string",
      "title_treatment_url": "string or null",
      "trailers": [
        {
          "url": "string",
          "profile": "string"
        }
        // ... more trailer objects
      ] or null,
      "design_variant": "string",
      "published": boolean,
      "total_items": integer,
      "web_url": "string",  // URL to the film group's web page
      "portrait_image": "string or null",
      "crm_image": "string or null",
      "is_season": boolean,
      "description": "string",
      "description_html": "string"
    }
    // ... more film group objects
  ],
  "meta": {
    "next_page": integer or null,
    "total_pages": integer,
    "total_count": integer
  }
}

```

##### Response Fields

- **id** (integer): Unique identifier for the film group.

- **slug** (string): URL-friendly identifier for the film group.

- **color** (string): Hexadecimal color code associated with the group (e.g., "fdd23b").

- **hide_title_on_splash** (boolean): Indicates whether the title should be hidden on the splash screen.

- **double_bill** (boolean): Indicates if the film group features a double bill (two films shown together).

- **full_title** (string): The full title of the film group.

- **full_title_upcase** (string): The full title in uppercase letters.

- **title** (string): The main title of the film group.

- **title_upcase** (string): The main title in uppercase letters.

- **title_color** (string): Hexadecimal color code for the title text.

- **subtitle** (string or null): The subtitle of the film group, if any.

- **subtitle_upcase** (string or null): The subtitle in uppercase letters.

- **subtitle_color** (string): Hexadecimal color code for the subtitle text.

- **average_colour_hex** (string): Average color of the film group's image in hexadecimal format.

- **focal_point** (object): Focal point for image display.

  - **x** (float): Horizontal focal point (0.0 to 1.0).

  - **y** (float): Vertical focal point (0.0 to 1.0).

- **image** (string): URL to the film group's image.

- **short_description** (string): A brief description of the film group.

- **short_description_html** (string): HTML-formatted short description.

- **title_treatment_url** (string or null): URL to a specialized title image, if available.

- **trailers** (array or null): An array of trailer objects, or null if none.

  - **Trailer Object**:

    - **url** (string): URL to the trailer video.

    - **profile** (string): Video quality profile (e.g., "240p", "720p", "1080p").

- **design_variant** (string): Design variant of the film group (e.g., "default").

- **published** (boolean): Indicates whether the film group is published and visible to users.

- **total_items** (integer): Total number of items (films) in the group.

- **web_url** (string): URL to the film group's page on MUBI's website.

- **portrait_image** (string or null): URL to a portrait version of the image, if available.

- **crm_image** (string or null): URL to the CRM (Customer Relationship Management) image, if available.

- **is_season** (boolean): Indicates if the film group represents a season (e.g., a series of films).

- **description** (string): Detailed description of the film group.

- **description_html** (string): HTML-formatted detailed description.



###### Meta Object



- **next_page** (integer or null): The number of the next page if there are more pages; null if there are no additional pages.

- **total_pages** (integer): Total number of pages available.

- **total_count** (integer): Total count of film groups available.



###### Notes



- **Nullable Fields**: Some fields can be null if the data is not available. These include subtitle, subtitle_upcase, title_treatment_url, trailers, portrait_image, and crm_image.

- **Image Fields**: The image, portrait_image, and crm_image fields are URLs pointing to images. They can be used to display visuals associated with the film group.

- **Trailers**: If trailers is not null, it contains an array of trailer objects, each with a url and profile.

- **Focal Point**: The focal_point object helps in displaying the image correctly by indicating the point of interest.

- **Descriptions**: Both short_description and description are provided in plain text and HTML formats, allowing for flexible display options.




##### Error Handling
```json
{
  "error": {
    "message": "string",
    "code": integer
  }
}
```

##### Notes
- **Authentication**: This endpoint does not require authentication; however, including client identification headers is recommended.
- **Pagination**: Use the page query parameter to navigate through pages. The meta.next_page field indicates if more pages are available.
- **Image Field Handling**: Since the image field can be either a string or an object, ensure your application checks the data type before accessing properties.



#### Get Collection Items

##### Endpoint

```bash
GET /v3/film_groups/{id}/film_group_items
```

##### Description

Retrieves items (films) within a specific collection.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Path Parameters

```
id: Collection ID
```

##### Query Parameters

```
include_upcoming: true (to include upcoming films)
page: Page number for pagination
per_page: Number of results per page (default: 24)
```

##### Response

```json
{
  "film_group_items": [ /* Array of film items */ ],
  "meta": { /* Metadata including next page */ }
}
```
- **film_group_items**: Array of film items.

    - **film**: Information about each film in the collection.
        - **id**: Film ID
        - **slug**: Unique identifier for the film's URL
        - **title**: Title of the film
        - **original_title**: The original language title
        - **year**: Release year
        - **duration**: Length of the film in minutes
        - **stills**: Various image sizes for the film's poster or stills.
            - **small, medium, retina, etc.**: Image URLs in different resolutions.
        - **average_colour_hex**: Hex color of the film's primary visual.
        - **trailer_url**: Optional URL of the film’s trailer.
        - **popularity**: Popularity score of the film.
        - **web_url**: Link to the film's page on Mubi.
        - **genres**: Array of film genres.
        - **average_rating**: Average rating of the film.
        - **content_rating**: Age rating information.
        - **directors**: Array of director(s) of the film.
        - **consumable**: Availability details of the film.
            - **available_at**: Date and time when the film became available.
            - **availability**: Film status (e.g., "live").
            - **permit_download**: Whether the film allows downloads.

- **meta**: Pagination metadata.

    - **current_page**: The current page of results.
    - **next_page**: The next page, if available.
    - **total_pages**: Total number of pages.
    - **total_count**: Total number of films.
    - **per_page**: Number of films returned per page.

### Cast Members

#### Browse Cast Members

##### Endpoint

```bash
GET /v3/browse/cast_members
```

##### Description

Retrieves a list of cast and crew members.

##### Headers

```
Standard headers for web client identification.
```

##### Query Parameters

```
Filters as needed (e.g., role)
page: Page number for pagination
```

##### Response

```json
{
  "cast_members": [ /* Array of cast member objects */ ],
  "meta": { /* Metadata including next page */ }
}
```



#### Get Cast Member Details

##### Endpoint

```bash
GET /v3/cast_members/{slug}
```

##### Description

Retrieves detailed information about a cast member.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Path Parameters

```
slug: Unique slug identifier for the cast member
```

##### Response

```json
{
  "name": "string",
  "image_url": "string",
  "credits": [ /* Array of credits */ ],
  /* Other details */
}
```



#### Get Films by Cast Member

##### Endpoint

```bash
GET /v3/cast_members/{id}/films
```

##### Description

Retrieves films associated with a cast member.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Path Parameters

```
id: Cast member ID
```

##### Query Parameters

```
cast_member_credit: Role or credit type
page: Page number for pagination
per_page: Number of results per page
```

##### Response

```json
{
  "films": [ /* Array of film objects */ ],
  "meta": { /* Metadata including next page */ }
}
```


### Lists

#### Browse Lists

##### Endpoint

```bash
GET /v3/browse/lists
```

##### Description

Retrieves user-created lists of films.

##### Headers

```
Standard headers for web client identification.
```

##### Query Parameters

```
sort: Sorting option
page: Page number for pagination
```

##### Response

```json
{
  "lists": [ /* Array of list objects */ ],
  "meta": { /* Metadata including next page */ }
}
```



#### Get List Films

##### Endpoint

```bash
GET /v3/lists/{slug}/list_films
```

##### Description

Retrieves films within a specific list.

##### Headers

```
Authorization: Bearer {token}
Additional headers.
```

##### Path Parameters

```
slug: Unique slug identifier for the list
```

##### Query Parameters

```
page: Page number for pagination
per_page: Number of results per page
```

##### Response

```json
{
  "list_films": [ /* Array of film objects */ ],
  "meta": { /* Metadata including next page */ }
}
```


### Industry Events
#### Browse Industry Events
```bash
GET /v3/browse/industry_events
```
Retrieves a list of industry events like festivals and awards.

#### Get Event Years
```bash
GET /v3/industry_events/{slug}/years
```
Retrieves available years for an industry event.

#### Get Event Films
```bash
GET /v3/industry_events/{id}/films
```
Retrieves films associated with a specific industry event.


### Additional Notes

- All endpoints require appropriate headers for authentication and client identification.
- Pagination is handled via the `page` query parameter and the `meta.next_page` field in responses.
- The `Authorization` header must include a valid Bearer token obtained during authentication.
- Errors are typically returned with an HTTP status code and a JSON body containing an error message.