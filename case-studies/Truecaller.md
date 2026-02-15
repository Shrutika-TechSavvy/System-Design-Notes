How Truecaller works- System design breakdown

The Truecaller is a global platform that provides caller identification , spam deetction and call blocking services. It achieves this by leveraging a massive database of phone numbers and user-contributed contact info .

The functional requirement of the system is :
 - Idntifying the caller name in  < 200 ms
 - Detect and block the spam calls
 - Allow user spam reporting
 - Store user profiles, etc
The non-functional requirement of the system is :
 - Ultra low latency

<img width="680" height="807" alt="image" src="https://github.com/user-attachments/assets/db527ef5-9b56-4a07-a7d5-51851a0f6f28" />
This is the figure of Truecaller's System design architecture - Ref : Ashish Mishra (https://www.linkedin.com/posts/imashishmishra_systemdesign-architecture-truecaller-activity-7204308517577441280-fX7l/). This post helps a lot to learn about the architecture of truecaller


Initially 
1. Client - > Load balancer + Rate limiter
   - At the top we have , GET called id(phone)#
   - When a user receives a call, the mobile app sends a request: GET /caller?phone=+91xxxxxx
   - This request then gies to
       - load balancer: It distrbutes the traffic across multiple servers and prevents a single server from being overloaded
       - Rate limiter : Prevents abuse (eg. bots sending millions of queries) , protects against DDoS attacks, limits requests per user/ IP
2. Two major paths in the diagram
   - Lookup service and 

Lookup service :
This handles GET caller id(phone)#



