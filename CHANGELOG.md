### CHANGELOG

This file documents important changes to the SignalFx Python client library.

- [[1.0.18] - 2018-03-15: Compression of datapoint payloads](#1018---2018-03-15-compression-of-datapoint-payloads)
- [[1.0.17] - 2018-03-02: Support for immediate SignalFlow results](#1017---2018-03-02-support-for-immediate-signalflow-results)
- [[1.0.16] - 2017-03-24: SignalFlow streaming performance](#1016---2017-03-24-signalflow-streaming-performance)
- [[1.0.15] - 2017-02-21: Preflight API](#1015---2017-02-21-preflight-api)
- [[1.0.14] - 2016-12-07: SignalFlow client bug fixes and context managers](#1014---2016-12-07-signalflow-client-bug-fixes-and-context-managers)
- [[1.0.13] - 2016-12-05: More features from detector APIs](#1013---2016-12-05-more-features-from-detector-apis)
- [[1.0.12] - 2016-11-28: Detector APIs](#1012---2016-11-28-detector-apis)
- [[1.0.11] - 2016-11-23: Long value support](#1011---2016-11-23-long-value-support)
- [[1.0.10] - 2016-11-21: Unicode event properties fix](#1010---2016-11-21-unicode-event-properties-fix)
- [[1.0.9] - 2016-10-26: Datapoints queue draining fix](#109---2016-10-26-datapoints-queue-draining-fix)
- [[1.0.8] - 2016-10-20: A missing field from events](#108---2016-10-20-a-missing-field-from-events)
- [[1.0.7] - 2016-10-05: More Python 3 compatibility](#107---2016-10-05-more-python-3-compatibility)
- [[1.0.5] - 2016-09-29: Python 3 compatibility](#105---2016-09-29-python-3-compatibility)
- [[1.0.1] - 2016-06-02: Support for SignalFlow API](#101---2016-06-02-support-for-signalflow-api)


#### [1.0.18] - 2018-03-15: Compression of datapoint payloads

The main change in 1.0.18 is that payloads of datapoints sent to
SignalFx will now be compressed by default (using GZip compression and
`Content-Encoding: gzip`). This can be disabled by specifying
`compress=False` on the SignalFx client, or on the ingest sub-client
directly.

#### [1.0.17] - 2018-03-02: Support for immediate SignalFlow results

Added support for the new `immediate` flag when executing SignalFlow
computation. Setting this flag to `true` forces the system to shift the
timerange of the computation by the `maxDelay` amount (either detected,
or specified), to ensure that the computation returns and completes
without additional delay to wait for late data.

Also added support in the library to access event metadata on events
received from a SignalFlow computation.

Updated the default TCP timeout to 5 seconds to match our Java and Ruby
libraries.

#### [1.0.16] - 2017-03-24: SignalFlow streaming performance

Added support for compressed SignalFlow WebSocket messages, which
improves the streaming performance by reducing the bandwidth
requirements of the client.

#### [1.0.15] - 2017-02-20: Preflight API

Added support for the detector preflighting API, allowing for the
execution of a detector program in a mode that simply summarizes the
events that would otherwise be generated, allowing for the quicker
execution of that preflighting over longer spans of historical data.

This release also includes a bugfix to how the total number of input
timeseries is calculated, as well as support for a new version of the
binary data message encoding (not yet used).

#### [1.0.14] - 2016-12-07: SignalFlow client bug fixes and context managers

Fixes a bug in the SignalFlow streaming computation client library that
would lead to an incomplete first data batch returned from the
computation stream when the program being executed as multiple published
streams.

All three sub-clients also now support Python context managers so they
can be used in `with` blocks:

```python
with signalfx.SignalFx().signalflow('MY_TOKEN') as flow:
    computation = flow.execute(program)
    for msg in computation.stream():
        # ...
```

#### [1.0.13] - 2016-12-05: More features from detector APIs

Added support for the `/v2/detector/validate` endpoint via
`rest.validate_detector()`, and support for searching detectors by tags
when using `rest.get_detectors()`.

It is also now possible to pass `ignore_not_found=True` to REST delete
operations to ignore failures on attempting to remove a non-existent
resource for which the DELETE call would otherwise return a 404.

#### [1.0.12] - 2016-11-28: Detector APIs

Added support for managing SignalFlow V2 detectors via the REST client.

#### [1.0.11] - 2016-11-23: Long value support

`long` type metric values were previously unsupported.  This release allows
int64 values and property values as defined by the protocol buffer. Values
greater than or equal to `-(2**63)` and less than or equal to `(2**63)-1`.
Values exceeding the specified boundaries will raise a ``ValueError`` exception.

Boolean property values were previously dispatched as integer values. This
release fixes this and emits boolean property values as a boolean type.

#### [1.0.10] - 2016-11-21: Unicode event properties fix

Unicode strings were previously unsupported for event properties. This release
allows event properties to be assigned unicode strings.

#### [1.0.9] - 2016-10-26: Datapoints queue draining fix

In certain situations, it was possible for the ingest client to stop and let the
program exit before the datapoints queue was fully drained to SignalFx. This
release fixes this and ensures that the background sending thread does not
prematurely exits before the queue is fully drained.

#### [1.0.8] - 2016-10-20: A missing field from events

Version 1.0.8 is a small point release to expose the EventTimeSeries ID from
events received from a SignalFlow v2 computation. This field can then be used to
lookup the metadata of that EventTimeSeries from the computation.

```python
c = flow.execute(program)
for msg in c.stream():
    if isinstance(msg, signalfx.signalflow.messages.EventMessage):
        pprint.pprint(c.get_metadata(msg.tsid))
```

#### [1.0.7] - 2016-10-05: More Python 3 compatibility

Version 1.0.7 includes an updated version of the generated ProtocolBuffer code,
generated with version 3 of the Protocol Buffer compiler and library, which
produces Python 3 compatible Python source code.

#### [1.0.5] - 2016-09-29: Python 3 compatibility

Version 1.0.5 of the SignalFx Python client library provides compatibility for
Python 3.x.

#### [1.0.1] - 2016-06-02: Support for SignalFlow API

In version 1.0.1 of this client, we introduced support for the SignalFlow API.
This means you can use this client to programmatically stream analytical
computations from SignalFx in real time, in addition to sending data in to
SignalFx. Using SignalFlow, you can build your own applications that leverage
SignalFx's streaming analytics outside the SignalFx UI. To read more about
SignalFlow, click here: https://developers.signalfx.com/docs/signalflow-overview

Adding support for SignalFlow required upgrades to this client that are
backwards-incompatible with previous versions. Customers who are upgrading from
version 0.3.9 or earlier must change how the client is instantiated in
application code, and how it is authorized.

##### 1. Client instantiation

Each client's features are now divided among data transmission to SignalFx
(`ingest`), metadata retrieval (`rest`), and data streaming from SignalFx to
your client (`signalflow`).  This means that when you instantiate a SignalFx
client object, you must also choose which SignalFx API you will access using
that object.

- If you use the client to send data to SignalFx, use `ingest`.
- If you use the client to retrieve metric names and metadata from SignalFx,
  use `rest`.
- To use the new SignalFlow API to stream analytics to your client,
  use `signalflow`.

Before SignalFlow support, client instantiation used to look like this:

```python
import signalfx

sfx = signalfx.SignalFx('ACCESS_TOKEN')
```

After SignalFlow support, client instantiation now looks like this:

```python
import signalfx

sfx = signalfx.SignalFx()

# To send data from client to SignalFx using the ingest API
ingest = sfx.ingest('API_SESSION_TOKEN')

# To get and set properties and tags using the REST API
rest = sfx.rest('USER_SESSION_TOKEN')

# To stream data from SignalFx to client using the SignalFlow API
flow = sfx.signalflow('USER_SESSION_TOKEN')
```


##### 2. Authenticating to SignalFx

As illustrated in the above example, instead of supplying an access token at the
moment of instantiating a SignalFx client object, you must now supply it when
you choose which API to access.

- For `ingest`, supply your API session token. Obtain this token from within the
  SignalFx app.
- For `rest` and `signalflow`, first authenticate with your SignalFx credentials,
  then supply your user session token. [Click here to read about SignalFx authentication](https://developers.signalfx.com/docs/authentication-overview).

You can authenticate using cURL as in the following example:

```python
curl -s -XPOST -HContent-Type:application/json https://api.signalfx.com/v2/session -d'{"email":"USERNAME","password":"PASSWORD"}' | jq -r '.accessToken'
```

Alternatively, you could authenticate to SignalFx programmatically from within
this client:

```python
import signalfx
sfx = signalfx.SignalFx()
token = sfx.login("USERNAME", "PASSWORD")
```
