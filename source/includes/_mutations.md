# Mutations

## CreateBookingQuote

A quote holds a set of inventory while locking in a price. You can think of a quote as a draft booking or a reservation.

There are times when something you thought would be available based on the Availability Calls is not available when making the quote.

This can happen for a few reasons:

1) The availability call was out of sync. Given this makes a real-time call to check the supplier's inventory, there's always a window where our initial call returned availability but this call didn't. Our goal is to make this window as small as possible

2) Certain suppliers have very complex rules on how they accept bookings. It might be that your specific tickets aren't available, or there are complex rules around time-cutoffs.

With those caveats aside, making these requests might look like this:

```graphql
mutation CreateQuote($request: BookingQuoteInput!) {
  createBookingQuote(quoteInput: $request) {
    bookingQuote {
      id
      priceBreakdown {
        total {
          formatted
        }
      }
    }
    errors {
      code
      detail
      value
    }
  }
}
```

>  With the variables formed something like this:

```javascript
{
	"request": {
		"availabilityTimeId": "at0apapwe6",
		"tickets": [
			{
				"quantity": 4,
				"ticketId": "t06rx6q"
			}
		]
	}
}
```

## UpdateBookingQuote

It is useful to update a quote as a customer changes the tickets, switches their desired time, etc.

This call is nearly identical to the CreateBookingQuote:

```graphql
mutation EditBookingQuote($request: EditBookingQuoteInput!) {
  editBookingQuote(input: $request) {
    bookingQuote {
      id
      priceBreakdown {
        total {
          formatted
        }
      }
    }
    errors {
      code
      detail
      value
    }
  }
}
```

>  With the variables formed something like this:

```javascript
{
	"request": {
		"bookingQuoteId": "qb0nm9r",
		"quoteInput": {
			"availabilityTimeId": "at0apapwe6",
			"tickets": [
				{
					"quantity": 5,
					"ticketId": "t06rx6q"
				}
			]
		}
	}
}
```

## CreateBooking

This will create a booking out of a provided quote. At this point, your booking is submitted and there's nothing left to do on your end.


```graphql
mutation CreateBooking($request: CreateBookingInput!) {
  createBooking(input: $request) {
    booking {
      id
    }
    errors {
      code
      detail
      value
    }
  }
}
```

>  With the variables formed something like this:

```javascript
{
	"request": {
		"bookingQuoteId": "qb0nm9r",
		"customer": {
			"name": "Peek Plus Demo",
			"email": "name@example.com",
			"phone": "0196488932",
			"country": "US",
			"postalCode": "12345"
		}
	}
}
```

## CancelBooking

This will cancel the booking in our system and notify the provider that the customer will no longer be coming. We are assuming a refund will be issued on your end and update the customer in our system to indicate a refund has been granted.


```graphql
mutation CancelBooking($input: CancelBookingInput!) {
  cancelBooking(input: $input) {
    booking {
      status
    }
    errors {
      code
      detail
      value
    }
  }
}
```

>  With the variables formed something like this:

```javascript
{
	"input": {
		"bookingId": "b0eb5p"
	}
}
```
