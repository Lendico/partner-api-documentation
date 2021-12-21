# Partner API
# Idempotency

Partner API supports idempotency for safely retrying requests avoiding performing the same operation twice, thus avoiding changing or duplicating resource accidentally. 

For example, you have submitted a request and a connection issue happened in the middle of the process, you do not have the response for the request, therefore you are not sure whether the request was properly processed, using the idempotency key you can easily retry and it will give you the same result whether it is the first time or the application was already created(no resource duplication).
So Idempotency helps when for some reason you are not sure whether you request was processed correctly or not, and you can safely retry it.

Idempotency can be achieved by providing the  additional header `Idempotency-Key: <key>` to the request 

Idempotency key is a unique value sent by the partner, that the server uses for identify requests. The idempotency key is a string type value, and we recommend to use V4 UUIDs as idempotency key for avoiding collision. Idempotency key cannot be equal for different requests.

The idempotency key are stored for at least 24h.

Post operation requires a `Idempotency-Key`.
