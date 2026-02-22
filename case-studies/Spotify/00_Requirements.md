Spotify System Design - Requirements
# Problem statement
We are designing a music streaming platform like Spotify.
ie. 
User opens app -> search or select a song _> Press play ->music starts almost instantly
But that behind simple buttin click, the sytem has to : 
 - Athenticate the user
 - Check if they are premium
 - Locate the song
 - Deliver it from the closest server
 - Track listening activity
 - Update recommendations
 - Handle millions of users doing this at ths same time
SO, the backend is extremely large and distributedd



Design a scalable, highly available music streamingnplatforms similar to spotify that allows user to :
 - Stream music on demand
 - search tracks, albums, artists
 - Create and manage playlists
 - Get personalized recommendations
 - Use offline downloads (Premiun users)
The system must support hundreds of millions of users globally with low latency and high availability.

# Assumptions & scale
To design properly , we define scale first:
| Metric                   | Assumption              |
| ------------------------ | ----------------------- |
| Total Registered Users   | 500 Million             |
| Daily Active Users (DAU) | 150 Million             |
| Concurrent Active Users  | 10–20 Million           |
| Songs in Catalog         | 100+ Million            |
| Avg Song Size            | 5 MB                    |
| Streams per Day          | ~2 Billion              |
| Peak QPS (Playback)      | ~200K–500K requests/sec |
That means storaga for songs alone: 100 MB  X 5 MB = 100 TB
With replication (for safety) = 1.5 PB
This is huge so a single server cannot handle this so we must think distriuted from day one.

