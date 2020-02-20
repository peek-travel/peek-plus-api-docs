
# Authentication

## JWT

When you are granted access to the API, you will be given an Api Key ("your-api-key") and a Secret ("your-secret"). Every request you make will require you to create a JWT that includes your API Key in the `sub` field and a unique ID in the `jti` field, which is then signed w/ your secret:

`{"sub": "your-api-key", "jti": "unique-id", "exp": 1516239022}`

If you're new to JWTs, [https://jwt.io/](https://jwt.io/) is a GREAT place to start:

![image](https://user-images.githubusercontent.com/1004167/74957531-87788100-53bc-11ea-9cde-1b3f2fe1c12b.png)

```elixir
defmodule Token do
  use Joken.Config

  @doc """
  Generate a signed token w/ default claims
  """
  def generate do
    {:ok, claims} =
      # NOTE: Joken automatically adds a uniq `jti` + `exp` for every token generated.
      Token.generate_claims(%{
        "sub" => "your-api-key"
      })

    {:ok, token, _claims} = encode_and_sign(claims, signer())

    token
  end

  defp signer, do: Joken.Signer.create("HS256", "your-secret")
end

Token.generate()
```

The reason we chose JWT is because there exists a client for (nearly?) every language imaginable. No need to write your own logic, just find the library (all listed on the above site) for your language and it should be straightforward from there.

We are an Elixir Shop, so our library of choice is called `Joken`; on the right is how you might choose to implement the above w/ `Joken`.

## Add Token as Header

The Token you generated above has to be included in the header for every request:

`Authorization: Bearer TOKEN`

<aside class="info">Replace TOKEN with your own token</aside>
<aside class="warning">Please keep your API credentials secret. Do not put it in client-side code.</aside>

