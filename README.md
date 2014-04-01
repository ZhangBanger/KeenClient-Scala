# keen-scala

**Note**: This library is currently in development and does not implement all of the features of the Keen API.
It's safe to use for single-event publishing. Bulk publishing and other API features will be added over time.

keen-scala is based on the [dispatch](http://dispatch.databinder.net/Dispatch.html)
asynchronous HTTP library. Therefore, all of the returned values are
`Future[Response]`.

The returned object is a [Response](http://sonatype.github.io/async-http-client/apidocs/reference/com/ning/http/client/Response.html)
from the async-http-client library. Normally you'll want to use `getResponseBody`
to get the response but you can also check `getStatusCode` to verify something
didn't go awry.

## JSON

Presently this library does **not** do any JSON parsing. It works with strings only. It is
assumed that you will parse the JSON returned and pass stringified JSON into any methods that
require it. Feedback is welcome!

## Dependencies

Depends on [spray](http://spray.io/) and
[grizzled-slf4j](http://software.clapper.org/grizzled-slf4j/). It's compiled for
scala 2.10.

## Notes

# Using It

You'll have to compile from source for now.

```
sbt package
```

# Testing It

This test suite includes integration tests which require keys and access to Keen IO's
API. You can skip them with

```
test-only * -- exclude integration
```

## Environment Variables

You'll want to set the following environment variables:

* KEEN_PROJECT_ID
* KEEN_MASTER_KEY
* KEEN_WRITE_KEY
* KEEN_READ_KEY

## Example

```
import io.keen.client.scala.Client

val client = new Client(
  masterKey = sys.env("KEEN_PROJECT_ID"),
  masterKey = sys.env("KEEN_MASTER_KEY"),
  writeKey = sys.env("KEEN_WRITE_KEY"),
  readKey = sys.env("KEEN_READ_KEY")
)


// Publish an event!
client.addEvent(
  collection = "collectionNameHere",
  event = """{"foo": "bar"}"""
)

// Publish an event and care about the result!
val resp = client.addEvent(
  collection = "collectionNameHere",
  event = """{"foo": "bar"}"""
)

// Add an onComplete callback for failures!
resp onComplete {
  case Success(r) => println(resp.getResponseBody)
  case Failure(t) => println(t.getMessage) // A Throwable
}

// Or use a map
resp map {
  println("I succeeded!")
} getOrElse {
  println("I failed :(")
}

```
