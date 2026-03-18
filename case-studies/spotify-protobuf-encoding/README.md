# Spotify Microservices Communication using Protobuf
## Case Study from Designing Data-Intensive Applications (Chapter 4)

This case study explores how large-scale systems like Spotify use binary encoding formats such as Protocol buffers for efficient service-to-service communication.

## 1. Problem :

Spotify operates thousands of microservices - The large system has different small functional , independant modules which in turn integrated with each together creates the system. These services communicate frequently to perform tasks such as :
 - fetching playlists
 - generating recommendations
 - retrieving user data
 - tracking playback events

 IWhat's the challenges ?
 These services must exchange stuctured data like :
 {
"user_id": "123",
"playlist_id": "rock_hits"
}

At firsy glance, formats like JSOn seem easy to use byt at Spotify scale, this creates serious problems ;
- Large data size : -> increases network usage
- Slower processing -> JSON parsing is expensive
- No strict schema -> different services may interpret data differently
- Difficult to evolve -> adding new fields can break older services

Why soes this matter then ?
Spotify continuously updates its system ,New features are added frequently (eg. recommendations based on mood , genre or listening history)
This means :
 - Data structures keep changing
 - Old and new version of the service must work together
So Spotify needs a way to send data efficiently , enforce a clear structure and safely evolve schemas over time.
so, How can services efficiently exchange structured data at massive scale while ensuring:
- high performance
- low network usage
- strong schema definition
- backward and forward compatibility

This is where binary encoding formats like **Protocol Buffers** come into the picture.

## 2. Why JSON is Not Enough ?

At small scale , JSON works well because it is simple and human readable. However at Spotify's scale , JSOn becomes inefficient.

Example JSON Request

{
"user_id": "123",
"playlist_id": "rock_hits"
}

Problems :
1. Large Payload size
   JSON includes field names as text , which increases the size of every request. For example: "user_id" is sent every time. This adds the unnecessary overhead. At millions of request per second , this leads to higher bandwidth usage and increased latency.
2. Slower processing
   JSON needs to be : parsed (string -> object), validates so this is a slower compared to binary formats.
3. Weak schema enforcement
   JSOn doesn't strictly enforce structure so it leads to problems of missing fields, incorrect data types, incosnsistent format across services.
4. Difficult Schema Evolution

If a new field is added:

{
"user_id": "123",
"playlist_id": "rock_hits",
"preferred_genre": "rock"
}

Older services might:
- ignore it incorrectly
- break if they expect a fixed structure

## 3. Why Spotify Uses Protocol Buffers

To solve these problems, Spotify uses Protocol Buffers (Protobuf).

Protobuf is a binary serialization format developed by Google.

Example Protobuf Schema

message PlaylistRequest {
string user_id = 1;
string playlist_id = 2;
}

Advantages of Protobuf
1. Compact Size
   - Field names are not sent.
   - Only field numbers are used
   - This reduces payload size significantly.
2. Faster Processing
   - Binary format → faster serialization/deserialization
   - Lower CPU usage
3. Strong Schema
   - Defined using .proto files
   - Ensures consistency across services
4. Easy Schema Evolution
   - New fields can be added safely:

message PlaylistRequest {
string user_id = 1;
string playlist_id = 2;
string preferred_genre = 3;
}

Old services:
- ignore unknown fields
- continue working without breaking

Key Insight (From DDIA Chapter 4)

- The goal is not just to encode data, but to ensure that:systems remain compatible over time, data can evolve without breaking services, This is why binary formats like Protobuf are widely used in real-world systems.

## **4. Deep Dive: Real Request Flow with Protobuf (Step-by-Step)**

**Step 1: User action (Client layer)**
- User clicks on playlist, Client prepares a request 
{
 "user_id" : "123", 
  "playlist_id" : "rock_hits"
 }
 - At this point Data is still in JSON/Object form, No protobuf yet
 
 **Step 2: Client Uses Protobuf Schema**
 - Before sending the request, the client uses a .proto definition because it is schema based encoding so it strictly tells that I want this format:

message PlaylistRequest {
string user_id = 1;
string playlist_id = 2;
}
This means that there is structure called PlaylistRequest, it must have user_id (text) , playlist_id (text) so basically any request must follow this strcuture. 
- The client library (generated from .proto) converts this into an internal object(We use the .proto file to create a structured object before converting it into binary.):

PlaylistRequest {
user_id = "123"
playlist_id = "rock_hits"
}
<img width="432" height="67" alt="image" src="https://github.com/user-attachments/assets/7fe9d015-c623-4121-9a98-ae01a3d4a2ec" />

In simple terms, the `.proto` file is like a fixed structure or template that both client and server already know. Using this, the client does NOT create JSON first—instead, it directly creates a structured object (like `PlaylistRequest`) using the format defined in the `.proto`. This object is just normal data inside the program, but it strictly follows the schema. So basically, Step 2 means: the client takes the `.proto` structure and creates data in that exact format (not JSON), which will later be converted into binary and sent to the server.


**Step 3: Protobuf serialization (Object -> Binary)**
Protobuf converts this object into binary using : Encoding rules

**Step 4: gRPC Transmission**
- After the data is converted into binary, the client needs to send it to the server over the internet. This is where gRPC comes in. Think of gRPC as a fast delivery system that knows how to send and receive Protobuf data.

- Instead of sending normal text data like in REST (JSON over HTTP), gRPC sends the binary data using HTTP/2, which is a more efficient version of HTTP.

**Step 5: Recommendation Service Receives Data**

The receiving service:
- Gets binary data
- Uses the SAME .proto schema
- Starts decoding

**Step 6: Protobuf Deserialization (Binary → Object)**
Reconstructed object:

PlaylistRequest {
user_id = "123"
playlist_id = "rock_hits"
}

**Step 7: Business Logic Execution**
Now the service:
- fetches user preferences
- computes recommendations
- prepares response
<img width="403" height="324" alt="image" src="https://github.com/user-attachments/assets/4640e7a9-c340-4dca-89ea-8f5b1174df78" />

<img width="250" height="222" alt="image" src="https://github.com/user-attachments/assets/2e66eb2b-d0dc-42dc-b6a8-1b631682b8c5" />
<img width="387" height="188" alt="image" src="https://github.com/user-attachments/assets/8f405c73-99d4-405b-b26a-c6c8ac7b726d" />

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e59652e0-6a13-4a92-8e64-2eb1ae554b95" />

