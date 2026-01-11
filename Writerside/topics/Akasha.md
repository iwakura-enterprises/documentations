<show-structure depth="2"/>

# Akasha

<img src="https://akasha.iwakura.enterprises/data-source/hetzner/public/logo/akasha.png" alt="Akasha logo" width="300" border-effect="rounded"/>

**Akasha** is a service that provides file access and sharing capabilities across multiple protocols such as SFTP and
other **over HTTP-based APIs**. The main goal of Akasha is to offer a **unified platform for managing and sharing files
securely and efficiently**. It is targeted at developers and tech-savvy users that do not need _fancy pancy_ web
interfaces, but rather a reliable and easy-to-use backend service.

Akasha is built with **security** and **privacy** in mind. Reading and writing files can be protected behind a
token-based
authentication system, ensuring that only authorized users or systems can access the data.

It also supports **multiple storage backends**, allowing you to define multiple so-called **data sources**. All this is
packaged in lightweight Java application with small memory footprint with also super-lightweight jar file size of ~150KB.

[Source Code](https://github.com/iwakura-enterprises/akasha) —
[Documentation](https://docs.iwakura.enterprises/akasha.html) —
[Akasha-API-Kirara Maven Central](https://central.sonatype.com/artifact/enterprises.iwakura/akasha-api-kirara)

Built using [Sigewine](https://docs.iwakura.enterprises/sigewine.html) —
[Jean](https://docs.iwakura.enterprises/jean.html) —
[Ganyu](https://docs.iwakura.enterprises/ganyu.html) —
[Kirara](https://docs.iwakura.enterprises/kirara.html)

> The logo you're seeing on this page? It is actually served using Akasha!
> 
> `https://akasha.iwakura.enterprises/data-source/hetzner/public/logo/akasha.png`

## Features

Serving files from multiple data sources over HTTP
: Define multiple data sources with different storage backends to be served over HTTP.

Authentication over tokens
: Allows you to secure access to your files using token-based authentication.

Intelligent MIME/Content-Type detection
: Automatically detects and sets the correct MIME type for served files based on their file extensions.

Respects `Accept` header
: Serves files in the format requested by the client, if supported (e.g., HTML, JSON or plain text).

Proper `InputStream` handling
: Efficiently manages file streams to optimize performance and resource usage. This means that large files can be
served without consuming excessive memory or disk space.

Careful path traversal protection
: Ensures that file access is restricted to the defined data sources, preventing unauthorized access.

Simple API
: Provides a straightforward read/write API for file operations.

Lightweight
: Small memory footprint without confusing installation or configuration requirements.

Prometheus metrics
: Exposes Prometheus-compatible metrics for monitoring and observability.

## Installation

<procedure>
<step>
Download the latest Akasha jar file from the <a href="https://github.com/iwakura-enterprises/akasha/releases">GitHub releases</a>
</step>
<step>
Download your favorite Java Runtime Environment (JRE) if you don't have one already. <b>Akasha requires at least Java 21.</b>
<a href="https://adoptium.net/temurin/releases?version=21">Temurin Adoptium is tested and works well,</a> but any
Java 21+ runtime should work.
</step>
<step>
Run Akasha with the following command: <code>java -jar akasha-x.y.z-all.jar</code>
</step>
<step>
Wait till the dependencies are downloaded. After that, Akasha will terminate automatically in order
for JVM to load them properly.
</step>
<step>
Repeat the 3rd step to generate default configuration files.
</step>
<step>
Configure Akasha by editing configuration files in the <code>configs/</code> directory.
</step>
<step>
Try accessing Akasha at <code>http://localhost:7000/data-source/{data-source}/{file-path}</code>
</step>
</procedure>

## Usage

After running Akasha, you can access files from your configured data sources using the following URL pattern:
`https://{hostname}/data-source/{data-source-name}/{file-path}`

If a data source path allows write access, you may also upload files using HTTP PUT requests to the same URL. This
requires you to specify attachment called `file` in the `multipart/form-data` body of the request.

### API Endpoints

<note>Accessing Akasha in a Java application? See <a href="#akasha-api-kirara">Akasha API wrapped in Kirara</a></note>

#### `GET /data-source/{name}/{file-path}`

Retrieves the specified file from the given data source.

The response will include the file's content in bytes.
Respects the `Accept` header for response format. If the file is not found or access is denied, appropriate HTTP status
codes will be returned.

> If the path requires authentication tokens, include them as query parameters like so:
`?token=your-token-here`

<tabs>

<tab title="200 OK (any format)">
Data in raw bytes.

The Content-Type header will depend on the file being served (fallbacks to application/octet-stream if unknown).
</tab>

<tab title="404 Not Found (application/json)">

```json
{
  "status": 404,
  "message": "File not found"
}
```

</tab>

<tab title="404 Not Found (text/html)">

```
HTTP Status 404

Error reading from data source: No such file

Akasha
```

</tab>

<tab title="404 Not Found (text/plain)">

```
Error 404: Error reading from data source: No such file
```

</tab>

<tab title="400 Bad Request (application/json)">

Status code 400 is used when the request is malformed, or you try to access a path that is not allowed.

```json
{
  "status": 400,
  "message": "Bad request"
}
```

</tab>

</tabs>

#### `PUT /data-source/{name}/{file-path}`

Uploads a file to the specified path in the given data source.

Accepts application/octet-stream with the file content in the request body.

The response will indicate success or failure of the upload operation.

> If the path requires authentication tokens, include them as query parameters like so:
`?token=your-token-here`

<tabs>

<tab title="200 OK (application/json)">

```json
{
  "status": 200,
  "message": "File written successfully"
}
```

</tab>

<tab title="200 OK (text/html)">

```
HTTP Status 200

File written successfully

Akasha
```

</tab>
<tab title="400 Bad Request (application/json)">

Status code 400 is used when the request is malformed, or you try to access a path that is not allowed.

```json
{
  "status": 400,
  "message": "Bad request"
}
```

</tab>
</tabs>

#### `DELETE /data-source/{name}/{file-path}`

Deletes file or directory at the specified path in the given data source. Directories must be
empty before deletion.

The response will indicate success or failure of the delete operation.

> If the path requires authentication tokens, include them as query parameters like so:
`?token=your-token-here`

<tabs>

<tab title="200 OK (application/json)">

```json
{
  "status": 200,
  "message": "File deleted successfully"
}
```

</tab>

<tab title="200 OK (text/html)">

```
HTTP Status 200

File deleted successfully

Akasha
```

</tab>
<tab title="400 Bad Request (application/json)">

Status code 400 is used when the request is malformed, or you try to access a path that is not allowed.

```json
{
  "status": 400,
  "message": "Bad request"
}
```

</tab>
</tabs>

#### `POST /data-source/{name}/{file-path}`

Allows you to manage the specified file in the given data source. The request body includes `ActionParametersDTO`
determining the action to perform. This action type then determines the response content.

| Action Type            | Response Content             | Description                                                                |
|------------------------|------------------------------|----------------------------------------------------------------------------|
| `FILE_INFO`            | `FileInfoActionResponseDTO`  | Retrieves metadata about the specified file.                               |
| `LIST_FILES`           | `ListFilesActionResponseDTO` | Lists files in the specified directory.                                    |
| `LIST_FILES_RECURSIVE` | `ListFilesActionResponseDTO` | Recursively lists files in the specified directory and its subdirectories. |

The response will indicate success or failure of the manage operation.

> If the path requires authentication tokens, include them as query parameters like so:
`?token=your-token-here`

<tabs>

<tab title="FILE_INFO 200 OK (file exists)">

```json
{
  "status": 200,
  "message": null,
  "fileInfo": {
    "name": "example.txt",
    "path": "/some/directory/example.txt",
    "sizeBytes": 12345,
    "type": "FILE"
  }
}
```

</tab>

<tab title="FILE_INFO 200 OK (file does not exist)">

```json
{
  "status": 200,
  "message": null,
  "fileInfo": null
}
```

</tab>

</tabs>

<tabs>

<tab title="LIST_FILES + LIST_FILES_RECURSIVE 200 OK">

```json
{
  "status": 200,
  "message": null,
  "files": [
    {
      "name": "example.txt",
      "path": "/some/directory/example.txt",
      "sizeBytes": 12345,
      "type": "FILE"
    },
    {
      "name": "another-example.txt",
      "path": "/some/directory/another-example.txt",
      "sizeBytes": 6789,
      "type": "FILE"
    },
    {
      "name": "subdirectory",
      "path": "/some/directory/subdirectory",
      "sizeBytes": 0,
      "type": "DIRECTORY"
    }
  ]
}
```

</tab>

</tabs>

## Configuration

### `data_sources.json`

Allows you to configure multiple data sources with different storage backends.

```json
{
  "validateWritePermissionEntriesHaveToken": true,
  "sources": [
    // ... data sources ...
  ]
}
```

`validateWritePermissionEntriesHaveToken`
: When true, Akasha will terminate if any write permission entry does not specify at least one token.

#### SFTP Data Source

This configuration defines an SFTP data source named "my-sftp-storage" that connects to an SFTP server at
`sftp.example.com` on port `22` using the provided username and password. It also specifies permission entries for three
paths:

- The `public/` path is read-only and does not require any tokens for access.
- The `private/` path allows write access but requires the token `my-secret-token` for authentication.
- The `metadata/data.json` file is read-only and requires the token `secret-data` for access.

```json
{
  "type": "SFTP",
  "name": "my-sftp-storage",
  "hostname": "sftp.example.com",
  "port": 22,
  "username": "username",
  "password": "fumofumo",
  "permission": {
    "entries": [
      {
        "path": "public/",
        "write": false,
        "tokens": []
      },
      {
        "path": "private/",
        "write": true,
        "tokens": ["my-secret-token"]
      },
      {
        "path": "metadata/data.json",
        "write": false,
        "tokens": ["secret-data"]
      }
    ]
  }
}
```

### `file_cache.json`

Configure file caching options to improve performance.

```json
{
  "enabled": true,
  "resetTtlOnAccess": true,
  "directory": "./file_cache/",
  "ttlSeconds": 3600,
  "maxSizePerFileBytes": 10485760,
  "maxTotalSizeBytes": 524288000,
  "httpCacheMaxAgeSeconds": 3600
}
```

`enabled`
: Enables or disables file caching.

`resetTtlOnAccess`
: If true, resets the TTL of cached files upon access.

`directory`
: Specifies the directory where cached files are stored.

`ttlSeconds`
: Sets the time-to-live for cached files in seconds.

`maxSizePerFileBytes`
: Defines the maximum size for individual cached files in bytes.

`maxTotalSizeBytes`
: Sets the maximum total size for all cached files in bytes. No new files will be cached if this limit would be reached
when adding the new file.

`httpCacheMaxAgeSeconds`
: Sets the `Cache-Control: max-age` header value for HTTP responses in seconds. This minimizes repeated requests from
clients.

### `javalin.json`

Configure Javalin server options.

```json
{
  "port": 7000,
  "maxRequestSizeBytes": 104857600
}
```

`port`
: Specifies the port on which the HTTP server listens for incoming requests.

`maxRequestSizeBytes`
: Sets the maximum allowed size for incoming HTTP requests in bytes. Requests exceeding this size will be rejected with a
`413 Payload Too Large` status code.

### `prometheus.json`

Configure Prometheus metrics options.

```json
{
  "enabled": true,
  "jvmMetricsEnabled": true,
  "bearerToken": "b4e27f8d-b089-4143-8457-265656b6acad"
}
```

`enabled`
: Enables or disables Prometheus `/metrics` endpoint.

`jvmMetricsEnabled`
: If true, JVM metrics such as memory usage and garbage collection stats are included in the Prometheus metrics.

`bearerToken`
: If set, requires this bearer token for accessing the `/metrics` endpoint.

## Akasha API Kirara

Akasha comes with a Java API wrapper built on top of [Kirara](https://docs.iwakura.enterprises/kirara.html) called
**Akasha API Kirara**. This library simplifies interaction with Akasha from Java applications.

### Installation {id="installation_akasha-api-kirara"}

Akasha API Kirara is available on Maven Central.

<include from="Maven-Versions.md" element-id="akasha_api_kirara_version"/>

> Java 8 or higher is required.

<note>
You might need to click the version badge to see the latest version.
</note>

<tabs>
    <tab id="gradle" title="Gradle">
        <code-block lang="groovy">
            implementation 'enterprises.iwakura:akasha-api-kirara:VERSION'
            // Kirara & JSON-compatible serializer
            implementation 'enterprises.iwakura:kirara-core:VERSION'
            implementation 'enterprises.iwakura:kirara-gson:VERSION'
        </code-block>
    </tab>
    <tab id="maven" title="Maven">
        <code-block lang="xml">
            <![CDATA[
                <dependency>
                    <groupId>enterprises.iwakura</groupId>
                    <artifactId>akasha-api-kirara</artifactId>
                    <version>VERSION</version>
                </dependency>
                <!-- Kirara & JSON-compatible serializer -->
                <dependency>
                    <groupId>enterprises.iwakura</groupId>
                    <artifactId>kirara-core</artifactId>
                    <version>VERSION</version>
                </dependency>
                <dependency>
                    <groupId>enterprises.iwakura</groupId>
                    <artifactId>kirara-gson</artifactId>
                    <version>VERSION</version>
                </dependency>
            ]]>
        </code-block>
    </tab>
</tabs>

### Usage {id="usage_akasha-api-kirara"}

Here's a simple example of how to use Akasha API Kirara to read and write files from/to an Akasha server:

```java
AkashaApi api = new AkashaApi(
  new HttpUrlConnectionHttpCore(),
  new GsonSerializer(),
  "http://localhost:7000"
);
api.setDefaultToken("write-access-token");

String text = "Hello, Akasha!";

AkashaResponse writeResult = api.write("hetzner", "/public/writable/test.txt", text.getBytes())
  .send()
  .join();
assertNotNull(writeResult);
assertEquals(200, writeResult.getStatus());

AkashaResponse readResult = api.read("hetzner", "/public/writable/test.txt")
  .send()
  .join();
assertNotNull(readResult);
assertEquals(200, readResult.getStatus());
assertEquals(text, new String(readResult.getContent()));
```

<warning>Do not forget to call <code>#send()</code> to send your requests.</warning>