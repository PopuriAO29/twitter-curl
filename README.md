# twitter-curl - A curl-like CLI Tool for the Twitter API

A command-line tool for interacting with the Twitter (formerly X) API, supporting both OAuth 1.0a and OAuth 2.0 authentication.

## Features

- OAuth 2.0 PKCE flow authentication
- OAuth 1.0a authentication
- Multiple OAuth 2.0 account support
- Persistent token storage
- HTTP request customization (headers, methods, body)

## Installation
```bash
curl -fsSL https://raw.githubusercontent.com/xdevplatform/twitter-curl/main/install.sh | sudo bash
```


## Usage

### Authentication

You must have a developer account and app to use this tool. 

#### App authentication:
```bash
twitter-curl auth app --bearer-token BEARER_TOKEN
```

#### OAuth 2.0 User-Context
**Note:** For OAuth 2.0 authentication, you must specify the redirect URI in the [Twitter API developer portal](https://developer.twitter.com/en/portal/dashboard).

1. Create an app at the [Twitter API developer portal](https://developer.twitter.com/en/portal/dashboard).
2. Go to authentication settings and set the redirect URI to `http://localhost:8080/callback`.
![Setup](./assets/setup.png)
![Redirect URI](./assets/callback.png)
3. Set the client ID and secret in your environment variables.
```env
export CLIENT_ID=your_client_id
export CLIENT_SECRET=your_client_secret
```
4. Get your access keys:
```bash
twitter-curl auth oauth2
```
#### OAuth 1.0a authentication:
```bash
twitter-curl auth oauth1 --consumer-key KEY --consumer-secret SECRET --access-token TOKEN --token-secret SECRET
```

### Authentication Management
View authentication status:
```bash
twitter-curl auth status
```

Clear authentication:
```bash
twitter-curl auth clear --all                       # Clear all tokens
twitter-curl auth clear --oauth1                    # Clear OAuth 1.0a tokens
twitter-curl auth clear --oauth2-username USERNAME  # Clear specific OAuth 2.0 token
twitter-curl auth clear --bearer                    # Clear bearer token
```

### Making Requests

Basic GET request:
```bash
twitter-curl /2/users/me
```

Custom HTTP method:
```bash
twitter-curl -X TWEET /2/tweets -d '{"text":"Hello world!"}'
```

Add headers:
```bash
twitter-curl -H "Content-Type: application/json" /2/tweets
```

Specify authentication type:
```bash
twitter-curl --auth oauth2 /2/users/me
twitter-curl --auth oauth1 /2/tweets
twitter-curl --auth app /2/users/me
```

Use specific OAuth 2.0 account:
```bash
twitter-curl --username johndoe /2/users/me
```

### Streaming Responses

Streaming endpoints (like `/2/tweets/search/stream`) are automatically detected and handled appropriately. The tool will automatically stream the response for these endpoints:

- `/2/tweets/search/stream`
- `/2/tweets/sample/stream`
- `/2/tweets/sample10/stream`
- `/2/tweets/firehose/strea/lang/en`
- `/2/tweets/firehose/stream/lang/ja`
- `/2/tweets/firehose/stream/lang/ko`
- `/2/tweets/firehose/stream/lang/pt`

For example:
```bash
twitter-curl /2/tweets/search/stream
```

You can also force streaming mode for any endpoint using the `--stream` or `-s` flag:
```bash
twitter-curl -s /2/users/me
```

### Media Upload

The tool supports uploading media files to the X API using the chunked upload process.

Upload a media file:
```bash
twitter-curl media upload path/to/file.mp4
```

With custom media type and category:
```bash
twitter-curl media upload --media-type image/jpeg --category tweet_image path/to/image.jpg
```

Check media upload status:
```bash
twitter-curl media status MEDIA_ID
```

Wait for media processing to complete:
```bash
twitter-curl media status --wait MEDIA_ID
```

#### Direct Media Upload

You can also use the main command with the `-F` flag for direct media uploads:

1. First, initialize the upload:
```bash
twitter-curl -X TWEET '/2/media/upload?command=INIT&total_bytes=FILE_SIZE&media_type=video/mp4&media_catefory=tweet_video'
```

2. Then, append the media chunks:
```bash
twitter-curl -X TWEET -F path/to/file.mp4 '/2/media/upload?command=APPEND&media_id=MEDIA_ID&segment_index=0'
```

3. Finally, finalize the upload:
```bash
twitter-curl -X TWEET '/2/media/upload?command=FINALIZE&media_id=MEDIA_ID'
```

4. Check the status:
```bash
twitter-curl '/2/media/upload?command=STATUS&media_id=MEDIA_ID'
```

## Token Storage

Tokens are stored securely in `~/.twitter-curl` in your home directory.

## Contributing
Contributions are welcome!

## License
This project is open-sourced under the MIT License - see the LICENSE file for details.
