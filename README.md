# Vertx
Vertx provides a **technology for content-based multimedia search** (query by example) in large-scale multimedia databases containing millions of items. It has the ability to operate on severely distorted media content (for example, camera jitter, screen with glare, acoustic noise) and can robustly identify content from multimedia clips.

**_You can use it:_**
  - *For copyright protection:* scan video and audio content against a database of copyrighted music, movies, and TV shows; find instantly any matching items in user uploaded clips.
  - *Live broadcast monitoring:* scan any live multimedia streams and find matches with broadcast TV or radio channels.
  - *On-device content identification:* identify audio and video content on a mobile device with no internet connection.

**_What to do next:_**
[//]: ############
  - Send us a [message](https://vertx.ai/#contact) to get your API key if you don't have it yet.
[//]: ############
  - Try the Vertx technology using your [preferred way](#try-vertx) and interpret the [obtained results](#interpreting-search-results)!
  - Explore the API through [Vertx Playground](https://portal.vertx.ai/playground)!
  - Read more about [our technology](#vertx-technology)!
  - Join [VertxAI Slack](https://join.slack.com/t/vertx-ai/shared_invite/zt-l2j9eett-5UYrpvy_cGp7OG~TgmFvhA) community to get help!
  - Explore our [subscription plans](#subscription-plans) and the limitations of our free public version!
  - [Contact us](https://vertx.ai/#contact) if you have any suggestions or specific requirements enquiries!


# Try Vertx
You can employ Vertx technology through a number of ways (**note that you must have a valid API token to make API requests!**):
- Through [REST API](#rest-api);
- Using [Vertx command line utility](#vertx-command-line-utility) operating through [gRPC](https://github.com/grpc/grpc) protocol;
- Through the [container interface](#container-interface) (we support Docker and Podman);
- iOS and Android SDKs.

To show the power of Vertx technology, we will use the following [video sample](https://youtu.be/QFz1yxtYYRQ) that has been *captured using a cellphone camera pointed at a TV screen* running "Troy" movie with some music in the background. You can download this `sample.mp4` file using a [direct link](https://github.com/vertxai/vertx_cli/releases/download/v1.0.2/sample.mp4).

Please refer to the [Vertx Playground](https://portal.vertx.ai/playground) for the complete list of available API end-points.


## REST API
The simplest way to start using Vertx technology is through REST API. You can employ REST API either by making requests using [Vertx Playground](https://portal.vertx.ai/playground) frontend or utilizing a command line utility like [curl](https://curl.se/).

If you choose this option, note that in this case, the media content is uploaded to our servers. There, audio and video fingerprints extracted from the uploaded content and matched against reference content. Therefore, REST API is a subject to [additional limitations](#subscription-plans).


### Vertx Playground Frontend
You can try Vertx Technology using our Playground. Visit [Vertx Playground](https://portal.vertx.ai/playground) to play with the API directly from your browser. This one-minute [video tutorial](https://www.youtube.com/watch?v=Ye3fP01fei4) shows the process in details.


### Example using curl
You can also use a command line utility like *curl* to directly invoke our API. For instance, a command to search for reference content using `sample.mp4` file as a query is provided below:

```console
$ curl -X POST "https://api.vertx.ai/v1/search/" -H "X-Session-Token: YOUR_VERTX_API_KEY" -F "media_file=@/path/to/sample.mp4" 
```


## Vertx Command Line Utility
One can utilize our command line tool to consume Vertx technology. This tool employs edge computing to extract fingerprints from media content and therefore, does not have additional limitations of REST API. 

Download the latest binary (currently, we provide a binary for the Linux x86-64 platform) from the [Github releases](https://github.com/vertxai/vertx_cli/releases) page and run the following command:

  - **Linux x86-64**
    ```console
    $ vertx --token YOUR_VERTX_API_KEY  sample.mp4
    ```


## Container Interface
You can also use the provided container image to run our command line tool on the platforms where it is not yet available. Please, install [docker](https://docs.docker.com/get-docker/) or [podman](https://podman.io/getting-started/installation) container engines for your operating system and run the following command:

```console
$ docker run -v $(pwd)/data:/data --rm vertxai/vertx_cli:latest /app/vertx --token YOUR_VERTX_API_KEY /data/sample.mp4
```

or

```console
$ podman run -v $(pwd)/data:/data --rm vertxai/vertx_cli:latest /app/vertx --token YOUR_VERTX_API_KEY /data/sample.mp4
```

This command will download the latest image with Vertx command line utility and run the container. It will mount the `./data/` subdirectory, where query multimedia items are put, as a `/data/` directory inside the container. Therefore, before running the command create a `./data/` subdirectory in your current directory and put there the multimedia items to query, e.g., `sample.mp4`. 


# Interpreting Search Results
All our tools deliver search results in JSON format. Each result contains the following JSON fields:

- `media_content` indicates type of the content being searched (audio or video);
- `source_path` - path of the source (query) file;
- `source_uid` - unique identifier of the source (query) file;
- `status` - status of the search call ("succeeded" or "failed"). 

Each result also contains a list of matches with the reference content: 
- `type` - type of the reference content (can be `"movie"` or `"music"`);
- `title` - title of the reference content (track or movie title);
- `uid` - a unique identifier of the reference content.

Metadata information specific to movies:
- `imdb_id` - IMDB identifier of the reference movie;
- `year` - release year of the movie.

Metadata information specific to music:
- `artist` - artist;
- `album` - album.

Every match contains the following information:
- `duration` - duration (in seconds) of the match segment;
- `que_offset` - the offset to the match segment in the input (query) file (in seconds);
- `ref_offset` - the offset to the match segment in the reference item (in seconds).


For instance, the resulting JSON document for the `sample.mp4` file is the following:

```json
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

As you can see, Vertx has found three matching segments between the content from the `sample.mp4` file and items from the public index: two movies and a song. Video content matched with the "Troy" movie, while audio content matched with the "Anxiety" music track by "Black Eyed Peas" and "Papa Roach". The same song has been also used as a soundtrack in "You Got Served" movie, that explains the presence of the second movie among the match results. 

Thus, you can see that Vertx has successfully found all reference items segments, both audio and video.



# Vertx Technology
Vertx technology is based on extracting digital signatures from audio and video content (called digital fingerprints) and adding them to a searchable index. Much like human fingerprints, digital fingerprints uniquely identify a chunk of audio/video content. Fingerprints are highly compressed when compared to the original media content, and it's not possible to restore the original content from the digital fingerprints.

The following figure shows the architecture of the Vertx system.

![Vertx Architecture](images/vertx.png "Vertx Architecture")

The system consists of **three main blocks**: Vertx cloud, Vertx backend and Vertx clients. Vertx cloud stores indices of reference content, and serves search and registration requests from Vertx backend. Vertx clients, which utilize REST API, upload media content for fingerprint extraction to the Vertx backend servers. Command line client utilities extract fingerprints by themselves. In that case, only extracted fingerprints are sent to the Vertx backend while the original content stays on-premise.   

All the reference content in the Vertx cloud is organized into buckets. *Buckets can be private or public.* By default, all search requests are sent to the public bucket. It contains fingerprints for millions of songs and thousands of movies. 

Private buckets contain the data of particular customers. It is available and managed only by this client.


# Subscription plans
Vertx API is provided **free of charge** with the following limitations:
- 5 API calls per 10 seconds;
- Search performed only in the public bucket;
- Private buckets functionality is not available.

Additional limitations on REST API:
- File size up to 100 MB;
- Media content length up to 600 seconds;

If you need higher quotas limits or private bucket search functionality, you have to choose **paid subscription plan**. Please, [contact us](https://vertx.ai/#contact) to discuss your needs and agree on the plan!
