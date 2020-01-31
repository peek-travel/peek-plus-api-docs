# Idempotency

Since every request that you make must contain a unique request id inside the signed token, if you make a second request w/ the same request id we will assume this you replaying a request and will return the original response.

<aside class="warning">
If you erroneously use the same request id for a request with a different request body, this will return an error.
</aside>
