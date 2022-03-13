---
title: "Server Sent Events With Jax-RS"
date: 2017-12-25T22:42:45Z
draft: true
---

Java EE 8 has been around for some time and it brought many improvements to the Java ecosystem and it’s many APIs. In this post we will talk about something that was brought by the fresh JAX-RS 2.1 specification, the Server-Sent Events.

Server-Sent Events (or SSE) are a one-way channel meant to send one to multiple messages from the server to the client(browser or java) in a single request. It’s built over HTPP and also has the capability to broadcast messages to event listeners registered on it.

## Structure

The events sent by SSE may consist of the following fields: event, data, id, retry and comment. The event field corresponds to a topic(as a topic in a MOM). The id field can be used to validate the order of the messages or to resend it when there is a connection issue, for example. The event payload is sent in the data field and must be text.The retry field is used to control reconnection and finally comment is a general purpose field.

## Server API

### Sending simple events

Take a look at the following code :

```
@GET
@Produces(MediaType.SERVER_SENT_EVENTS)
public void toString(@Context SseEventSink eventSink, @Context Sse sse) {
    try(SseEventSink sink = eventSink){
        sink.send(sse.newEvent("data"));
        sink.send(sse.newEvent("MyEventName","more data"));

        OutboundSseEvent event = sse.newEventBuilder().
            id("EventId").
            name("EventName").
            data("Data").
            reconnectDelay(10000).
            comment("Anything i wanna comment here!").
            build();

        sink.send(event);

    }
}
```

A JAX-RS resource method that produces **"text/event-stream"** (the same as MediaType.SERVER_SENT_EVENTS) and injects a **SseEventSink** is a SSE Resource method. There is no need for a return type because the SseEventSink object is considered the return type.

The **@Produces** annotation tells the caller we're gonna send a stream of events. We receive a **SseEventSink** object that is our outbound event stream and an instance of an event factory called **Sse**.

With the **Sse** object we can create the events on some different ways. We can create a new event passing only the data parameter, we can create a event passing the name and the **data** parameters or we can use the eventBuilder that Sse provides us.

### Send a broadcast event

Now, Let's analyse the following code :

```
@Singleton
public class SSEBroadcasting {

    @Context
    private Sse sse;

    private volatile SseBroadcaster sseBroadcaster;

    @PostConstruct
    public void init(){
        this.sseBroadcaster = sse.newBroadcaster();
    }

    @GET
    @Produces(MediaType.SERVER_SENT_EVENTS)
    public void register(@Context SseEventSink eventSink){
        eventSink.send(sse.newEvent("Welcome!"));
        sseBroadcaster.register(eventSink);
    }

    @POST
    @Produces(MediaType.SERVER_SENT_EVENTS)
    public void broadcast(@FormParam("event") String event){
        sseBroadcaster.broadcast(sse.newEvent(event));
    }
```

We can create an event broadcaster and send messages to all the SseEventSinks registered on it calling the method broadcast.

In this case, there is no need to receive Sse since we can inject it directly in the object and it is thread-safe.

## Client API
### Java API

The Java API may look like this:

```
public void connectToServer() {

    Client client = ClientBuilder.newClient();
    WebTarget target = client.target("http://localhost:8080/events/broadcast");
    
    try (SseEventSource source = SseEventSource.target(target).build()) {
        source.register(System.out::println);
        source.open();
        Thread.sleep(500);
    } catch (InterruptedException e) {

    }
}
```

The entry point to the client API is the SseEventSource class. It registers a target and a listener to the incoming events. In this case, we are registering the System.out.println() method that will print all the event that arrives in the next 500 milisseconds as defined in Thread.sleep(500).

## Conclusion

Server-side events are a useful way to push information from the server to another sources, without a request for that information.This post was heavily based on the JAX-RS API documentation so i suggest you take a look at it. The source code of this post is available in this github repo.

