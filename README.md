# PrivX SDK for Go

PrivX is a lean and modern privileged access management solution to automate your AWS, Azure and GCP infrastructure access management in one multi-cloud solution. This Software Development Kit (SDK) offers a high-level abstraction to programmatically configure your PrivX instances.

[![PrivX API](https://img.shields.io/badge/privx%20api-%20≥%2015-blue)](https://apispecs.ssh.com)
[![Documentation](https://godoc.org/github.com/SSHcom/privx-sdk-go?status.svg)](http://godoc.org/github.com/SSHcom/privx-sdk-go)
[![Build Status](https://img.shields.io/github/workflow/status/SSHcom/privx-sdk-go/Go)](https://github.com/SSHcom/privx-sdk-go/actions)
[![Git Hub](https://img.shields.io/github/last-commit/SSHcom/privx-sdk-go.svg)](https://github.com/SSHcom/privx-sdk-go/actions)
[![Coverage Status](https://coveralls.io/repos/github/SSHcom/privx-sdk-go/badge.svg?branch=master)](https://coveralls.io/github/SSHcom/privx-sdk-go?branch=master)
[![Go Report Card](https://goreportcard.com/badge/github.com/SSHcom/privx-sdk-go)](https://goreportcard.com/report/github.com/SSHcom/privx-sdk-go)


\
**Jump To**:
[PrivX REST API Reference](https://privx.docs.ssh.com/reference)


## Getting Started

The latest version of SDK is available at `master` branch of the repository. All development, including new features and bug fixes, take place on the `master` branch using forking and pull requests as described in contribution guidelines.

**Work in Progress**

### Instantiate SDK Client

PrivX SDK composes API client from three independent layers:
* `restapi` generic HTTPS transport layer
* `oauth` implements OAuth2 access token grant flows
* `api/...` type-safe implementation of PrivX API 

Here is a typical workflow required to setup the client:

```go
// 1. Create Authorizer and Access Token Provider
auth := oauth2.WithClientID(
	restapi.New(/* use restapi options to config http */),

	// Use oauth2 options to config authorizer
	oauth2.Access(/* ... */),
	oauth2.Secret(/* ... */),
)

// 2. Create HTTP transport for PrivX API
curl := restapi.New(
	restapi.Auth(provider),

  // Use other transport options
)

// 3. Create API client of rolestore feature
client := rolestore.New(curl)
```

### SDK Configuration providers

As application developers you have three options to configure PrivX SDK
* explicitly
* using config files
* using environment variable

It is possible to cascade configurations.

```go
// 1. Explicit configuration
curl := restapi.New(restapi.BaseURL(/* value */))

// 2. Use config files
curl := restapi.New(restapi.UseConfigFile(/* path to file */))

// 3. Environment variable
curl := restapi.New(restapi.UseEnvironment())

// 4. Cascade the configuration
curl := restapi.New(
	// attempt to read data from config file
	restapi.UseConfigFile(/* path to file */),
	// attempt to read environment
	restapi.UseEnvironment(),
	// attempt to fetch data from command line flags 
	restapi.BaseURL(/* command line value */)
)
```

Please see available config option for [restapi](restapi/opts.go) and [oauth](oauth/opts.go).

PrivX SDK `UseConfigFile` support following config file format

```conf
[api]

# restapi.BaseURL(...)
base_url="https://your-instance.privx.io"

# restapi.X509(...)
api_ca_crt=""" PEM certificate chain """

[auth]

# oauth.Access(...)
api_client_id="00000000-0000-0000-0000-000000000000"

# oauth.Secret(...)
api_client_secret="some-random-base64"

# oauth.Digest(...)
oauth_client_id="privx-external"
oauth_client_secret="another-random-base64"
```

PrivX SDK `UseEnvironment` support following environment variables

```bash
# restapi.BaseURL(...)
export PRIVX_API_BASE_URL=https://your-instance.privx.io

# oauth.Access(...)
export PRIVX_API_CLIENT_ID=00000000-0000-0000-0000-000000000000

# oauth.Secret(...)
export PRIVX_API_CLIENT_SECRET=some-random-base64

# oauth.Digest(...)
export PRIVX_API_OAUTH_CLIENT_ID=privx-external
export PRIVX_API_OAUTH_CLIENT_SECRET=another-random-base64
```

### Identity and Access Management

Usage of PrivX SDK requires API credential, which are available from your PrivX deployment: Settings > API Clients > Add API Client. Authorizer implement OAuth2 Resource Owner Password Grant

```go
auth := oauth.WithClientID(/* ... */)
```

Alternatively, you can use api client on behalf of existing user using its credentials. Authorizer implements OAuth2 Authorization Code Grant

```go
auth := oauth.WithCredential(/* ... */)
```

If your app needs to implement a flexible auth strategy that supports both. Use following method, it dynamically chooses a right strategy depending of available credentials  
```go
auth := oauth.With(/* ... */)
```

## Bugs

If you experience any issues with the library, please let us know via [GitHub issues](https://github.com/SSHcom/privx-sdk-go/issues). We appreciate detailed and accurate reports that help us to identity and replicate the issue.

* **Specify** the configuration of your environment. Include which operating system you use and the versions of runtime environments.

* **Attach** logs, screenshots and exceptions, in possible.

* **Reveal** the steps you took to reproduce the problem, include code snippet or links to your project.


## How To Contribute

The project is [Apache 2.0](LICENSE) licensed and accepts contributions via GitHub pull requests:

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request


## License

[![See LICENSE](https://img.shields.io/github/license/SSHcom/privx-sdk-go.svg?style=for-the-badge)](LICENSE)
