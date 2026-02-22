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

# Functional requirements (What the System must do )
**3.1 User Accounts**
User should be able to: 
	- Register
	- login
	- Maintain Profile
	- Subscribe to premium
So in simple we need aithentication and subscriptio validation

**3.2 Play Music**
When a user clicks play:
  - Song should start quickly, no buffering delay
  - Users should not be able to pause or resume
  - User should seek to any position
  - Premium users should download songs offline, 
So, playback must be fast, reliable, adaptive to network speed.
- T**his means we need streaming instead of full file download , adaptive bitrate and possibly CDN.**

**3.3 Search**
Users must be able to search:
  - Song name
  - Artist
  - Album
  - Playlist
Search should be fast (<200ms ideally), so we need the indexed search system and ranking logic.

**3.4 Playlist management**
Users can:
  - Create playlist
  - Add/remove songs
  - Reorder songs
  - Share playlists
- This means we need a database that stores playlist metadata, it must support frequent writes, it should handle collaborative edits

**3.5 Recommendations**
Spotify is not just a music player
It recommends:
  - daily mixes
  - Discover weekly
  - Similar artists
  - Recently played
This means, We must track user listening behavior, store massive event logs, run ML models and generate personalized feeds. Eventually this introduces the concepts of data pipelines (A system that collects this raw activityand processes it andd uses it to generate insights or recommendations) , event streaming(CCTV cameras recording everything) and Offline (We train machine learning models using historical data ie. we may take last 6 months listening history andall user bahvior to train model) + online(Fast and real-time ie. uses recent activity and updates fetaures quickly, make fast predictions) ML systems.


**3.6 Telemetry & Analytics**
Every time user:
  - Plays song
  - Skips
  - Searches
  - Likes a track, 
We must record that.
Why?
Because:
  - It improves recommendations
  - It helps analytics
  - It helps business decisions

This means:
We need event streaming architecture (like Kafka).
