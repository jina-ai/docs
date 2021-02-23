## Introduction

###### Jinabox is a lightweight, customizable front end for searching text, images, videos, audio or any kind of data with a Jina backend.

Ready to learn Jina? Read our [101 tutorials](https://101.jina.ai).


![jina box](jinabox.gif)


#### Features

- Easy-to-use web component for Jina search backend
- Users can drag and drop multi-modal data as search queries
- Visualize search results in a dropdown list or separate container
- Highly customizable

#### Before you start

Jinabox is a frontend for Jina. You will need to spin up a Jina backend to get search results. Based on what you want to search, run one of our example Jina backends:

- [Pokedex](https://github.com/jina-ai/examples/tree/master/pokedex-with-bit) (real image data): `docker run -p 65481:65481 -e "JINA_PORT=65481" jinaai/hub.app.bitsearch-pokedex search`

#### Open jinabox.js in your Browser

Just go to https://jina.ai/jinabox.js/ in your browser to open up the search interface.


#### Set Server Endpoint

Based on the Docker port you are exposing (i.e. 65481 or 45678 in above Docker images), set jinabox's server endpoint to:

    http://localhost:<port_number>/api/search


![server_endpoint](server_endpoint.png)

#### Start Searching!

Drag and drop images, video, or audio, or type in text to search.

![jina box_search](jinabox_search.gif)


### Jinabox in your Project

This is how you can add Jinabox to your existing web project.

#### Jinabox on [GitHub](https://github.com/jina-ai/jinabox.js/)


#### Installation via package manager

    npm install jinabox

or

    yarn add jinabox


#### Use Jinabox via Script Tag

In HTML, include the minified script:


    <script src="https://unpkg.com/jinabox"></script>
    <script>
        jb = window.JinaBox
        jb.init('http://0.0.0.0:65481/api/search');  // http://0.0.0.0:65481/api/search is the endpoint of the REST gateway of a Jina backend.
    </script>

Then create a container where the searchbar/floater is to appear:

    <jina-searchbar></jina-searchbar>

    <!--or-->

    <jina-floater></jina-searchbar>

Now you can drag anything from anywhere (local/browser/webpage) to it and conduct the search.

#### Supported MIME types

| MIME type                                                                       | Input (Query)              | Output (Result)                         | Description                                                                                                                                                                                                                  |
| ---                                                                             | ---                        | ---                                     | ---                                                                                                                                                                                                                          |
| [`text`](https://www.iana.org/assignments/media-types/media-types.xhtml#text)   | Typing                     | List only                               | Text-only data including any human-readable content, source code, or textual data such as comma-separated value (CSV) formatted data. Examples include `text/plain`, `text/csv`, and `text/html`.                         |
| [`image`](https://www.iana.org/assignments/media-types/media-types.xhtml#image) | Drag-and-drop              | `<img>` Containers in list/grid view    | Image or graphical data including both bitmap and vector still images as well as animated versions of still image formats like animated GIF or APNG. Common examples are `image/jpeg`, `image/png`, and `image/svg+xml`. |
| [`video`](https://www.iana.org/assignments/media-types/media-types.xhtml#video) | Drag-and-drop, webcam      | `<video>`  containers in list/grid view | Video data or files, such as MP4 movies (`video/mp4`).                                                                                                                                                                       |
| [`audio`](https://www.iana.org/assignments/media-types/media-types.xhtml#audio) | Drag-and-drop, webcam, mic | `<audio>`  containers in list view      | Audio or music data. Examples include `audio/mpeg`, `audio/vorbis`.                                                                                                                                                          |



#### Configuration


Play with and preview jinabox configurations [here](https://jina.ai/jinabox.js/)

| Setting                    | Default             | Type    | Description                                                                                                                                                 |
|----------------------------|---------------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `theme`                    | `default`           | string  | Color theme: `default`, `persian`, `pompelmo`, `honeybee`                                                                                                   |
| `searchIcon`               | `color`             | string  | Searchbar icon: `color`, `mono`, `inverse`, or a URL                                                                                                        |
| `showDropzone`             | `true`              | boolean | Show expanding drop zone when dragging files                                                                                                                |
| `resultsLocation`          | `dropdown`          | string  | Where to display results: `dropdown` or `external`. When selecting external, results will be rendered in the element corresponding to the `results-area-id` |
| `resultsAreaId`            | `jina-results-area` | string  | ID of where results will be rendered if `resultsLocation` is set to `external`                                                                              |
| `typewriterEffect`         | `false`             | boolean | Enable typewriter effect on the placeholder                                                                                                                 |
| `typewriterDelayItem`      | `1000`              | number  | Time (ms) delay between every placeholder switch when `typewriterEffect` is enabled                                                                         |
| `typewriterDelayCharacter` | `50`                | number  | Time (ms) delay between each character when `typwriterEffect` is enabled                                                                                    |
| `userMediaHeight`          | `500`               | number  | Webcam feed and capture height                                                                                                                              |
| `userMediaWidth`           | `300`               | number  | Webcam feed and capture width                                                                                                                               |
| `acceptAudio`              | `true`              | boolean | Allow search with audio queries                                                                                                                             |
| `acceptVideo`              | `true`              | boolean | Allow search with video queries                                                                                                                             |
| `acceptText`               | `true`              | boolean | Allow search with text queries                                                                                                                              |
| `acceptImage`              | `true`              | boolean | Allow search with image queries                                                                                                                             |

#### Expected Response Structure

`jinabox.js` expects query responses to contain either of the following structures

```json
{
  "search": {
    "docs": [
      {
        "topkResults": [
          {
            "matchDoc": {
              "docId": 14704,
              "weight": 1,
              "uri": "data:image/png;charset=utf,...",
              "mimeType":"image/png"
            }
          }
        ],
        "uri":"data:image/jpeg;...",
        "mimeType":"image/jpeg"
      }
    ]
  }
}

```
or

```json
{
  "search": {
    "docs": [
      {
        "matches": [
          {
            "docId": 14704,
            "weight": 1,
            "uri": "data:image/png;charset=utf,...",
            "mimeType":"image/png"
          }
        ],
        "uri":"data:image/jpeg;...",
        "mimeType":"image/jpeg"
      }
    ]
  }
}

```

#### Browser Support

Jinabox runs on modern browsers including Chrome, Firefox, Safari (desktop and mobile) and Opera. Certain features (like recording audio/video) are not available on all browsers and jinabox will make these features available accordingly.

### Troubleshooting

#### SSL Error on Localhost

Try `http://localhost:65481/api/search`, make sure to use `http` and `localhost`, instead of `0.0.0.0`.

#### Requests hanging or failing

Query your api with `curl` to ensure it is properly receiving, processing, and responding to requests. Ensure responses meet `jinabox` [expected response structure](#expected-response-structure).
