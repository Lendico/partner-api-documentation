# Partner API
# Idempotency

Partner API supports idempotency for safely retrying requests avoiding performing the same operation twice, thus avoiding changing or duplicating resouce accidentally. 

For example, you have submitted a request and a connection issue happened in the middle of the process, you do not have the response for the request, therefore you are not sure whether the request was properly processed, using the idepotency key you can easyly retry and it will give you the same result whether it is the first time or the application was already created(no resource duplication).
So idepotency helps when for some reason you are not sure whether you request was processed correctly or not, and you can safelly try it again.

Idepodency can be achievied by providing the  additional header `Idempotency-Key: <key>` to the request 


Idempotency key is a unique value sent by the partner, that the server uses for identify requests. The idepotency key is a string type value, and we recomend to use V4 UUIDs as idepotency key avoiding colision. Idepotency key cannot be equal for different requests.

Keys are pruned of the AP *To be added frequency of prunning keys*

Post operation requires a `Idempotency-Key`.
