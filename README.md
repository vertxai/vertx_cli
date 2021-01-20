# TL;DR
If you do not want to spend the next five minutes going through the documentation and want to start using Vertx API immediately, open your terminal and start identifying your media content right away:
```bash
curl -X POST "https://api.vertx.ai/v1/search/" -H "X-Session-Token: YOUR_VERTX_API_KEY" -F "media_file=@/path/to/media_file.mp4" 
```
- Send us a [message](https://vertx.ai/#contact) to get your API key if you don't have it yet.
- Visit [Vertx Playground](https://portal.vertx.ai/playground) to play with the API from your browser following this one minute [video tutorial](https://www.youtube.com/watch?v=Ye3fP01fei4).
- Join [VertxAI Slack](https://join.slack.com/t/vertx-ai/shared_invite/zt-l2j9eett-5UYrpvy_cGp7OG~TgmFvhA) community to get help.

# Vertx Technology
Vertx technology facilitates content-based audio/video search (query by example) in large-scale multimedia databases containing millions of items. 
It works amazingly fast, allowing one to identify any piece of content within a blink of an eye. 
It has the ability to operate on severely distorted media content (for example, camera jitter, screen with glare, acoustic noise) and can robustly identify content from video clips. 
Check out this [YouTube video](https://youtu.be/QFz1yxtYYRQ) that was captured using a cellphone camera pointed at a TV screen running "Troy" movie and some music in the background.
Vertx is able to scan the audio and video content of the clip against multimedia databases containing millions of songs and thousands of movies and successfully identify all matches in a second. 

This video will be used in API search  request examples throughout the rest of the documentation.
The video file `sample.mp4` is included into [Vertx command line utility](https://github.com/vertxai/vertx_cli/releases) release distribution. 
It can also be downloaded using [direct link](https://github.com/vertxai/vertx_cli/releases/download/v1.0.2/sample.mp4)   

[//]: ############

![alt text](images/troy.jpeg "Scene from \"Troy\" movie.")

[//]: ############

# Vertx architecture
Vertx technology is based on extracting digital signatures from audio and video content (called digital fingerpints) and adding them to a searchable index. 
Much likely like human fingerprints, digital fingerprints uniquely identify a chunk of audio/video content.
Fingerprints are highly compressed when compared to the original media content and it's not possible to restore the original content from digital fingerprints.

The three components of the system are Vertx cloud, Vertx backend and Vertx clients. 
Vertx backend contains indices of reference content and serves search and registration requests from Vertx backend.
Vertx clients upload media content for fingerprint extraction to the Vertx backend servers using Vertx REST API. 
Fingerprint extraction can also happen on the client side, an example being [Vertx command line utility](https://github.com/vertxai/vertx_cli/releases). 
In that case, extracted fingerprints are sent to the Vertx backend servers and original media content is not being uploaded.
Metadata for matched reference content is returned to the client.

![alt text](images/vertx.png "Vertx architecture")

## Content buckets
All the reference content in the Vertx cloud is organized into buckets. Buckets can be private or public.
By default, all search requests are sent to public bucket. It contains fingerprints for millions of songs and thousands of movies. 
Private buckets contain content registered by a customer and can only be accessed and managed by that customer.

## Vertx API
There are a number of ways to search, register, and manage audio and video content:
- REST API 
- [GRPC](https://github.com/grpc/grpc) API through [Vertx command line utility](https://github.com/vertxai/vertx_cli/releases)
- iOS and Android SDKs

# REST API
REST API is the simplest way to start using Vertx technology. Original media files are uploaded to the Vertx backend servers. 
Audio and video fingerprints extracted from the uploaded content are matched against reference content in the Vertx cloud.    

You must have a valid API token to make API requests. To authenticate a request, you should include the API token in the Authorization header.
```bash
X-Session-Token: YOUR_VERTX_API_KEY
```

## Usage example
A CURL example of searching `sample.mp4` file in the public bucket is provided below.
```bash
curl -X POST "https://api.vertx.ai/v1/search/" -H "X-Session-Token: YOUR_VERTX_API_KEY" -F "media_file=@sample.mp4" 
```
One can find the complete set of API calls and try them in the [Vertx Playground](https://portal.vertx.ai/playground). 

# Vertx command-line utility
To start using vertx command line utility, you must have a valid API key. To authenticate a request, you should set `token` flag.

```bash
--token YOUR_VERTX_API_KEY
```

The latest binary for Linux x86_64 platform can be downloaded from [Github releases](https://github.com/vertxai/vertx_cli/releases) section.
Vertx command line utility extracts fingerprints form media content and sends them to the Vertx backend. 
Received fingerprints are uploaded to the Vertx cloud and all the found matches are returned to the client.    

## Usage example
Usage example of vertx command-line utility is provided below. File path to a query multimedia file has to be specified in addition to the `--token` flag.  
```bash
vertx --token YOUR_VERTX_API_KEY  sample.mp4                
```

Search results are printed to stdout in JSON format. To store results into a file, stdout has to be redirected.
```bash
vertx --token YOUR_VERTX_API_KEY sample.mp4 > /tmp/search_result.json
```

# Interpreting results
Both, REST API and Vertx command line utility deliver search results in JSON format. The result for `sample.mp4` is provided below. 

```bash
[
  {
    "matches": [
      {
        "album": "Elephunk",
        "artist": "Black Eyed Peas; Papa Roach",
        "segments": [
          {
            "duration": 24.9375,
            "que_offset": 0.0,
            "ref_offset": 18.0
          }
        ],
        "title": "Anxiety",
        "type": "music",
        "uid": 6475547275973858650
      },
      {
        "imdb_id": 365957,
        "segments": [
          {
            "duration": 24.9375,
            "que_offset": 0.0,
            "ref_offset": 3500.625
          }
        ],
        "title": "You Got Served",
        "type": "movie",
        "uid": 1566530810344932800,
        "year": 2004
      }
    ],
    "media_type": "audio",
    "source_path": "sample.mp4",
    "source_uid": 1758730481226206085,
    "status": "succeeded"
  },
  {
    "matches": [
      {
        "imdb_id": 332452,
        "segments": [
          {
            "duration": 21.0,
            "que_offset": 0.0,
            "ref_offset": 8608.5
          }
        ],
        "title": "Troy",
        "type": "movie",
        "uid": 1342729426672482861,
        "year": 2004
      }
    ],
    "media_type": "video",
    "source_path": "sample.mp4",
    "source_uid": 1758730481226206085,
    "status": "succeeded"
  }
]
```

The content from `sample.mp4` file matched with three items from the index: two movies and a song. 
Video content matched with the "Troy" movie, while audio content matched with the "Anxiety" music track by "Black Eyed Peas" and "Papa Roach". 
The song was also used as a soundtrack in "You Got Served" movie. Original audio track from the "Troy" movie was replaced with a soundtrack from a different movie. 
Vertx was able to successfully identify all the matching segments, both audio and video.

## JSON output format
Each result contains the following JSON fields:

- `media_content` indicates type of the content being searched (audio or video)
- `source_path` path of the source (query) file
- `source_uid` unique identifier of the source (query) file
- `status` status of the search call ("succeeded" or "failed"). 

Each result also contains a list of matches with the reference content. 
- `type` type of the reference content (can be `"movie"` or `"music"`)
- `title` title of the reference content (track or movie title)
- `uid` a unique identifier of the reference content

Metadata information specific to movies:
- `imdb_id` IMDB identifier of the reference movie
- `year` movie release year 

Metadata information specific to music:
- `artist` artist
- `album` album

Every match contains the following information:
- `duration` match duration (in seconds)
- `que_offset` match offset in the input (query) file (in seconds)
- `ref_offset` match offset in the reference item (in seconds)


# Running vertx command-line utility from a docker container
Vertx command-line utility can also be executed in any environment that supports docker runtime. 
The steps required are:
- Install docker for your OS environment. Instructions can be found [here](https://docs.docker.com/get-docker/).
- Create a "data" folder which will be accessible from a docker container

```bash
mkdir data
```
- Copy `sample.mp4` file from the release package into "data" folder
```bash
cp sample.mp4 ./data
```
- Pull docker image containing vertx command line utility
```bash
docker pull vertxai/vertx_cli:latest
``` 
- Run vertx command-line utility from the container
  
```bash
docker run -v $(pwd)/data:/data --rm vertxai/vertx_cli:latest /app/vertx --token YOUR_VERTX_API_KEY /data/sample.mp4
```

# Subscription plans
Vertx API is provided free of charge with the following limitations:
- The uploaded file size limit through REST API is 100 Mb. Files larger that 100 Mb are rejected by the Vertx backend. Vertx command-line utility does not enforce any size limits.
- Maximum media content length per REST API call is 600 seconds. Everything beyond the first 600 seconds will be ignored.
  If a larger media file has to be processed, it has to be split into multiple chunks using such tools as [FFmpeg](https://ffmpeg.org/)
- The following rate limits are enforced:
    - 5 API calls per 10 seconds
    - 50000 requests per 24 hours
    - That totals to 8300 hours of analyzed content per day
- Private buckets are not available and no custom media content can be registered.

Paid subscription plans are available if higher quotas limits are requested.
