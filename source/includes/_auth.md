
# Authentication

## JWT

When you are granted access to the API, you will be given an Api Key ("your-api-key") and a Secret ("your-secret"). Every request you make will require you to create a JWT, that includes your API Key in the `sub` field and a unique request ID in the `reqid` field. Which is the signed w/ your Secret:

`{"sub": "your-api-key", "reqid": "unique-request-id"}`

If you're new to JWTs, [https://jwt.io/](https://jwt.io/) is a GREAT place to start:

![image](https://user-images.githubusercontent.com/1004167/73561655-38b77700-440e-11ea-8ea7-f00068dc1750.png)

```elixir
defmodule Token do
  use Joken.Config

  @doc """
  Given a unique request ID, return a signed token.
  """
  def for_request_id(request_id) do
    {:ok, claims} =
      Token.generate_claims(%{
        "sub" => "your-api-key",
        "reqid" => request_id
      })

    {:ok, token, _claims} = encode_and_sign(claims, signer())

    token
  end

  defp signer, do: Joken.Signer.create("HS256", "your-secret")
end

Token.for_request_id("unique-request-id")
```

The reason we chose JWT is because there exists a client for (nearly?) every language imaginable. No need to write your own logic, just find the library (all listed on the above site) for your language and it should be straightforward from there.

We are an Elixir Shop (don't even get me started), so our Library of choice is called `Joken`; on the right is how you might choose to implement the above w/ Joken.

## Add Token as Header

The Token you generated above has to be included in the header for every request:

`Authorization: Bearer TOKEN`

<aside class="info">Replace TOKEN with your own token</aside>
<aside class="warning">Please keep your API credentials secret. Do not put it in client-side code.</aside>

