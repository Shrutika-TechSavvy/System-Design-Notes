# Spotify Microservices Communication using Protobuf
## Case Study from Designing Data-Intensive Applications (Chapter 4)

This case study explores how large-scale systems like Spotify use binary encoding formats such as Protocol buffers for efficient service-to-service communication.

## 1. Problem :

Spotify operates thousands of microservices - The large system has different small functional , independant modules which in turn integrated with each together creates the system. These services communicate frequently to perform tasks such as :
 - fetching playlists
 - generating recommendations
 - retrieving user data
 - tracking playback events

 If JSON were used for every request: 
 - payload sizes would increase
 - latency would incereate
 - schema consistency would be different 
