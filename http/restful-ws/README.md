# HTTP Protocol Binding for Jakarta RESTful Web Services

For Maven based projects, use the following to configure the CloudEvents Jakarta RESTful Web Services Binding:

```xml
<dependency>
    <groupId>io.cloudevents</groupId>
    <artifactId>cloudevents-http-restful-ws</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</dependency>
```

## Receiving CloudEvents

You need to configure the `CloudEventsProvider` to enable marshalling/unmarshalling of CloudEvents.

Below is a sample on how to read and write CloudEvents:

```java
import io.cloudevents.CloudEvent;

import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.Path;
import javax.ws.rs.core.Response;

@Path("/")
public class EventReceiverResource {

    @GET
    @Path("getMinEvent")
    public CloudEvent getMinEvent() {
        return CloudEvent.buildV1()
            .withId("hello")
            .withType("example.vertx")
            .withSource(URI.create("http://localhost"))
            .build();
    }

    // Return the CloudEvent using the HTTP binding structured encoding
    @GET
    @Path("getStructuredEvent")
    @StructuredEncoding("application/cloudevents+csv")
    public CloudEvent getStructuredEvent() {
        return CloudEvent.buildV1()
            .withId("hello")
            .withType("example.vertx")
            .withSource(URI.create("http://localhost"))
            .build();
    }

    @POST
    @Path("postEventWithoutBody")
    public Response postEvent(CloudEvent inputEvent) {
        //Handle the event
        return Response.ok().build();
    }
}
```

## Sending CloudEvents

You need to configure the `CloudEventsProvider` to enable marshalling/unmarshalling of CloudEvents.

Below is a sample on how to use the client to send a CloudEvent:

```java
import io.cloudevents.CloudEvent;
import io.cloudevents.http.restful.ws.CloudEventsProvider;

import javax.ws.rs.client.Entity;
import javax.ws.rs.client.WebTarget;
import javax.ws.rs.core.HttpHeaders;
import javax.ws.rs.core.Response;

public class CloudEventSender {

    public Response sendEvent(WebTarget target, CloudEvent event) {
        return target
            .path("postEvent")
            .request()
            .buildPost(Entity.entity(event, CloudEventsProvider.CLOUDEVENT_TYPE))
            .invoke();
    }

    public Response sendEventAsStructured(WebTarget target, CloudEvent event) {
        return target
            .path("postEvent")
            .request()
            .buildPost(Entity.entity(event, "application/cloudevents+json"))
            .invoke();
    }

    public CloudEvent getEvent(WebTarget target) {
        Response res = target
            .path("getEvent")
            .request()
            .buildGet()
            .invoke();

        return res.readEntity(CloudEvent.class);
    }

}
```
