# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

# Unreleased

## Added

- None.

## Changed

- None.

## Removed

- None.

## Fixed

- The CORS layer / service methods `allow_headers`, `allow_methods`, `allow_origin`
  and `expose_headers` now do nothing if given an empty `Vec`, instead of sending
  the respective header with an empty value

# 0.2.2 (February 8, 2022)

## Fixed

- Add `Vary` headers for CORS preflight responses ([#216])

[#216]: https://github.com/tower-rs/tower-http/pull/216

# 0.2.1 (January 21, 2022)

## Added

- Support `Last-Modified` (and friends) headers in `ServeDir` and `ServeFile` ([#145])
- Add `AsyncRequireAuthorization::layer` ([#195])

## Fixed

- Fix build error for certain feature sets ([#209])
- `Cors`: Set `Vary` header ([#199])
- `ServeDir` and `ServeFile`: Fix potential directory traversal attack due to
  improper path validation on Windows ([#204])

[#145]: https://github.com/tower-rs/tower-http/pull/145
[#195]: https://github.com/tower-rs/tower-http/pull/195
[#199]: https://github.com/tower-rs/tower-http/pull/199
[#204]: https://github.com/tower-rs/tower-http/pull/204
[#209]: https://github.com/tower-rs/tower-http/pull/209

# 0.2.0 (December 1, 2021)

## Added

- **builder**: Add `ServiceBuilderExt` which adds methods to `tower::ServiceBuilder` for
  adding middleware from tower-http ([#106])
- **request_id**: Add `SetRequestId` and `PropagateRequestId` middleware ([#150])
- **trace**: Add `DefaultMakeSpan::level` to make log level of tracing spans easily configurable ([#124])
- **trace**: Add `LatencyUnit::Seconds` for formatting latencies as seconds ([#179])
- **trace**: Support customizing which status codes are considered failures by `GrpcErrorsAsFailures` ([#189])
- **compression**: Support specifying predicates to choose when responses should
  be compressed. This can be used to disable compression of small responses,
  responses with a certain `content-type`, or something user defined ([#172])
- **fs**: Ability to serve precompressed files ([#156])
- **fs**: Support `Range` requests ([#173])
- **fs**: Properly support HEAD requests which return no body and have the `Content-Length` header set ([#169])

## Changed

- `AddAuthorization`, `InFlightRequests`, `SetRequestHeader`,
  `SetResponseHeader`, `AddExtension`, `MapRequestBody` and `MapResponseBody`
   now requires underlying service to use `http::Request<ReqBody>` and
   `http::Response<ResBody>` as request and responses ([#182]) (BREAKING)
- **set_header**: Remove unnecessary generic parameter from `SetRequestHeaderLayer`
  and `SetResponseHeaderLayer`. This removes the need (and possibility) to specify a
  body type for these layers ([#148]) (BREAKING)
- **compression, decompression**: Change the response body error type to
  `Box<dyn std::error::Error + Send + Sync>`. This makes them usable if
  the body they're wrapping uses `Box<dyn std::error::Error + Send + Sync>` as
  its error type which they previously weren't ([#166]) (BREAKING)
- **fs**: Change response body type of `ServeDir` and `ServeFile` to
  `ServeFileSystemResponseBody` and `ServeFileSystemResponseFuture` ([#187]) (BREAKING)
- **auth**: Change `AuthorizeRequest` and `AsyncAuthorizeRequest` traits to be simpler ([#192]) (BREAKING)

## Removed

- **compression, decompression**: Remove `BodyOrIoError`. Its been replaced with `Box<dyn
  std::error::Error + Send + Sync>` ([#166]) (BREAKING)
- **compression, decompression**: Remove the `compression` and `decompression` feature. They were unnecessary
  and `compression-full`/`decompression-full` can be used to get full
  compression/decompression support. For more granular control, `[compression|decompression]-gzip`,
  `[compression|decompression]-br` and `[compression|decompression]-deflate` may
  be used instead ([#170]) (BREAKING)

[#106]: https://github.com/tower-rs/tower-http/pull/106
[#124]: https://github.com/tower-rs/tower-http/pull/124
[#148]: https://github.com/tower-rs/tower-http/pull/148
[#150]: https://github.com/tower-rs/tower-http/pull/150
[#156]: https://github.com/tower-rs/tower-http/pull/156
[#166]: https://github.com/tower-rs/tower-http/pull/166
[#169]: https://github.com/tower-rs/tower-http/pull/169
[#170]: https://github.com/tower-rs/tower-http/pull/170
[#172]: https://github.com/tower-rs/tower-http/pull/172
[#173]: https://github.com/tower-rs/tower-http/pull/173
[#179]: https://github.com/tower-rs/tower-http/pull/179
[#182]: https://github.com/tower-rs/tower-http/pull/182
[#187]: https://github.com/tower-rs/tower-http/pull/187
[#189]: https://github.com/tower-rs/tower-http/pull/189
[#192]: https://github.com/tower-rs/tower-http/pull/192

# 0.1.2 (November 13, 2021)

- New middleware: Add `Cors` for setting [CORS] headers ([#112])
- New middleware: Add `AsyncRequireAuthorization` ([#118])
- `Compression`: Don't recompress HTTP responses ([#140])
- `Compression` and `Decompression`: Pass configuration from layer into middleware ([#132])
- `ServeDir` and `ServeFile`: Improve performance ([#137])
- `Compression`: Remove needless `ResBody::Error: Into<BoxError>` bounds ([#117])
- `ServeDir`: Percent decode path segments ([#129])
- `ServeDir`: Use correct redirection status ([#130])
- `ServeDir`: Return `404 Not Found` on requests to directories if
  `append_index_html_on_directories` is set to `false` ([#122])

[#112]: https://github.com/tower-rs/tower-http/pull/112
[#118]: https://github.com/tower-rs/tower-http/pull/118
[#140]: https://github.com/tower-rs/tower-http/pull/140
[#132]: https://github.com/tower-rs/tower-http/pull/132
[#137]: https://github.com/tower-rs/tower-http/pull/137
[#117]: https://github.com/tower-rs/tower-http/pull/117
[#129]: https://github.com/tower-rs/tower-http/pull/129
[#130]: https://github.com/tower-rs/tower-http/pull/130
[#122]: https://github.com/tower-rs/tower-http/pull/122

# 0.1.1 (July 2, 2021)

- Add example of using `SharedClassifier`.
- Add `StatusInRangeAsFailures` which is a response classifier that considers
  responses with status code in a certain range as failures. Useful for HTTP
  clients where both server errors (5xx) and client errors (4xx) are considered
  failures.
- Implement `Debug` for `NeverClassifyEos`.
- Update iri-string to 0.4.
- Add `ClassifyResponse::map_failure_class` and `ClassifyEos::map_failure_class`
  for transforming the failure classification using a function.
- Clarify exactly when each `Trace` callback is called.
- Add `AddAuthorizationLayer` for setting the `Authorization` header on
  requests.

# 0.1.0 (May 27, 2021)

- Initial release.

[CORS]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
