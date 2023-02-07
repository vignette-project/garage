# Extension System: Remote Repository Specification

*Author: [@JcdeA](https://github.com/JcdeA) and [@sr229](https://github.com/sr229)*

## Table of Contents
1. [Introduction](#Introduction)
2. [Implementation](#Implementation)

## Introduction
In part of making Vignette accessible, Vignette will support binaries that extend the functionality of the base client through extensions and downloading them through a registry or a repository. The behavior of the extension repository mirrors that of Helm Charts or NuGet registries, and will not be limited to a single centralized repository to allow mirroring capabilities and alternative implementations of the repository.

## Implementation
The base implementation of a Vignette repository will be a plain HTTP(S) file server. This has many benefits, such as the ease of utilizing a CDN, or simply using GitHub Pages to serve the files.

There are certain restrictions to the structure of the filesystem, and the filenames.

### Filesystem Structure
Each VAST Extension should be stored in the following structure:
```
extensions
   |__ publisher.extname-version.vast
   |__ publisher2.extname-version.vast
```
The publisher name, version number, extension name should match the following regular expression: `^[a-zA-Z0-9-_.]+$`.

### Communicating with Vignette

Vignette communicates with these repositories through an `index.json` exposed at the root path of your repository or your hostname. It may look like this:

- `https://hello.example.com/index.json`
- `https://vignetteapp.github.io/sample-extension/index.json`
- `https://vignetteapp.org/extensions/index.json`

The user will provide the host base URL for your repository, and Vignette will proactively scan for the index file to validate if it's a valid repository.

**Note: Vignette does not provide safety gurantees for third-party repositories. As such, providing a warning for including third-party repositories will be included.**

#### index.json

`index.json` is the entrypoint for the Vignette client to validate and query if it is a valid repository. `index.json` may look like the following for static HTTP servers:

```json
{
  "version" : "1",
  "name": "Vignette Sample HTTP Repository",
  "description": "Vignette sample repository for static hosted extensions",
  "hasApi" : false
}
```

This is a rather simple API that tells Vignette that this repository has no dynamic endpoint and will search from the base URL for the `extensions/` directory. For servers with restful endpoints, it will look like the following:

```json
{
  "version": "1",
  "name" : "Vignette",
  "description" : "The official Vignette extension repository.",
  "hasApi": true,
  "resources" : [
     {
       "@id" : "https://id.vignetteapp.org/api/v1/login/",
       "@type" : "UserAuthentication"
    },
    {
      "@id": "https://vignetteapp.org/extensions/query",
      "@type": "SearchQueryService",
      "comment" : "Search service provided by the endpoint for RC clients."
    },
    {
      "@id" : "https://static.vignetteapp.org/extensions",
      "@type" : "PackageBaseAddress",
      "comment" : "the base URL to where to download extensions from. This is imilar to a HTTP(S) repository albiet it only provides the artifacts."
    }
  ]
}
```

This is similar to a NuGet repository where a API endpoint will provide API resources for Vignette to query upon on. Currently, the v1 spec included in this Specification should only support the following:

- `UserAuthentication` 
   - An OAuth2.0 endpoint for the User to autenticate to. Should return a valid JWT token for Vignette to use for querying the repository.

- `SearchQueryService`
  - The search service used by the search toolbar or as a query endpoint to look for a package. The endpoint on a null request must return all packages and must support search filters prepended by the `@` symbol (ex: `@publisher=vignetteapp`).

- `PackageBaseAddress`
  - This is where Vignette will download the artifacts. Similar to the plain HTTP(S) server, it will look for the extension via the `extensions/` directory. The format will be `extensions/{publisher}.{extname}.vast`.repository