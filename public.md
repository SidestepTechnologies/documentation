# Sidestep API guide

The Sidestep API can be used to get a list of touring artists, their shows and products which are available for pre-order.

## Contents:
1. [Displaying Artists](public.md#displaying-artists)
2. [Getting Merchandise for Artists and Tours](public.md#going-further-merchandise-for-tours-and-shows)

## API Token

The every request to Sidestep API should include a header `X-SIDESTEP-APP-TOKEN` with a token of your Sidestep application. Please contact matt@sidestepapp.com if you haven't received your token yet.

## Displaying artists

Pre-ordering  merchandise is available for artists that are currently touring. If an artist is not currently touring with Sidestep, they will not be returned in the response. This list is maintained by Sidestep and it can be fetched with:

#### Request
##### Params
- referrer - referrer is an optional parameter which is used to generate
ready-to-use artist URLs. URLs are returned in the `sidestep_webstore_url` attribute.

```sh
curl --request GET \
  --url https://production.sidestepapp.com/api/artists/store?referrer=sidestep \
  --header 'x-sidestep-app-token: your-sidestep-app-token'
```

#### Response

The response contains a list of currently touring artists (with Sidestep):

```json
{
  "artists": [
    {
      "id": 1,
      "created_at": "2017-01-21T01:04:05.992Z",
      "updated_at": "2017-01-21T01:11:52.185Z",
      "image": "https://production.sidestepapp.com/uploads/artists/8f1c2620-3831-43fa-8d97-e9c616204a82.jpg",
      "order": null,
      "slug": "panic-at-the-disco",
      "title": "Panic! At The Disco",
    }
  ]
}
```

You are probably interested in the following attributes:

Attribute             | Description
:-------------------- | :------------------------------------------------------------------------------------
id                    |
title                 |
image                 | An URL to artist's image. A size is 640x320 pixels.
sidestep_webstore_url | A ready-to-use URL of the artist store on shopsidestepapp.com. Use it in `<a\>` tags.

## Going further. Merchandise for tours and shows

### Getting current tours

A list of current tours is available at `artists/:artist_id/tours`. Sidestep API uses tours to group shows within one or multiple countries having the same currency. For example, an artist's tour in US & Canada is represented as two tour objects: one for US shows and one for Canada shows. Each tour has it's own set of products.

Tours can be fetched with:

```sh
curl --request GET \
  --url https://production.sidestepapp.com/api/artists/1/tours \
  --header 'x-sidestep-app-token: your-sidestep-app-token'
```

```json
{
  "tours": [
    {
      "id": 48,
      "created_at": "2016-02-03T22:49:56.779Z",
      "updated_at": "2016-02-03T22:49:56.779Z",
      "title": "Wintour is Coming (Spring 2016)",
      "state": "active",
      "currency": "usd",
      "artist_id": 1
    }
  ]
}
```

Attribute | Description
:-------- | :----------------------------------------------------------------------------
id        |
title     |
currency  | ISO 3-letter currency code. Use it to format product prices within this tour.

### Merchandise for a tour

The endpoint `artists/:artist_id/store/products?tour_id=:tour_id` can be used to fetch a list of products for the tour.

#### Request
##### Params
- tour_id - ID of a tour. Use `artists/:artist_id/tours` to fetch a list of
active tours.
- referrer - referrer is an optional parameter which is used to generate
ready-to-use product URLs. URLs are returned in `sidestep_webstore_url` attribute.

```sh
curl --request GET \
  --url 'https://production.sidestepapp.com/api/artists/1/store/products?tour_id=1&referrer=sidestep' \
  --header 'x-sidestep-app-token: your-sidestep-app-token'
```

#### Response

```json
{
  "products": [
    {
      "id": 43,
      "created_at": "2014-06-10T05:20:27.000Z",
      "updated_at": "2015-05-04T19:54:16.189Z",
      "description": null,
      "order": null,
      "price": "35.0",
      "square_image": "https://sidestep-assets-production.s3.amazonaws.com/uploads/products/50c8ce1d-8ca1-47a5-bf3d-275fec119252.jpg",
      "title": "SR&R Long Sleeve Shirt",
      "artist_id": 1,
      "tour_id": 1
    },
    ...
  ]
}
```

Product Attributes: 

Attribute             | Description
:-------------------- | :----------------------------------------------
id                    |
price                 | Product price in the currency used for the tour
title                 |
square_image          | URL to the product image. The size is 640x640 pixels.
sidestep_webstore_url | A ready-to-use URL of the artist store page on shopsidestepapp.com. Use it in `<a\>` tags.*

* The  URL in `sidestep_webstore_url` points to the artist's store (not to a product).

### Shows

Every tour has a set of shows which can be retrieved via `tours/:tour_id/upcoming_shows`

```sh
curl --request GET \
  --url 'https://production.sidestepapp.com/api/tours/48/upcoming_shows' \
  --header 'x-sidestep-app-token: your-sidestep-app-token'
```

```json
{
  "shows": [
    {
      "id": 920,
      "created_at": "2016-02-03T22:35:47.900Z",
      "updated_at": "2016-03-28T19:03:00.137Z",
      "show_datetime": "2016-03-28T02:00:00.000Z",
      "currency": "usd",
      "bandsintown_id": 11153039,
      "show_group_id": null,
      "venue_id": 508,
      "artist_ids": [
        1
      ],
      "tour_ids": [
        52
      ],
      "tag_ids": []
    }
  ],
  "venues": [
    {
      "id": 623,
      "created_at": "2016-04-29T21:05:49.058Z",
      "updated_at": "2016-04-29T21:05:49.058Z",
      "title": "Barclays Center",
      "city": "Brooklyn",
      "region": "NY",
      "country": "United States",
      "latitude": "40.683265",
      "longitude": "-73.97619",
      "time_zone_name": "America/New_York",
    },
  ]
}
```

Show Attributes:

Attribute     | Description
:------------ | :-----------------------------------------------------------------------------------------
id            |
show_datetime | ISO 8601 formatted date & time in UTC. Use `Venue.time_zone_name` to display in local time
currency      | ISO 3-letter currency code. Use it to format product prices within this tour.
venue_id      | ID of a venue. Venues are always included into the response under the root key `venues`

Venue attributes:

Attribute      | Description
:------------- | :-------------------------------------------------------------------------------
id             |
title          |
city           |
region         | An abbreviation of a state. Only for `United States`
country        | A name of a country
time_zone_name | IATA time zone name. Use it to display `Show.show_datetime` in a local timezone.

#### Important thing to know about date× of shows

Date and time of shows are usually displayed in the local timezone of the venue. It's very similar to how flight arrival & departure times are always displayed in local times.

Sidestep API always returns IATA time zone name in `time_zone_name` attribute of a venue object. An example value of this attribute is `America/New_York`. IATA timezone name can be used to convert a value in `show_datetime` to a local time.

### Merchandise for a show

Sidestep API has a very handy endpoint if there's a need to get products which are available for a particular show `artists/:artist_id/store/products?delivery_option_id=:delivery_option_id`.

#### Request
##### Params
- delivery_option_id - the word `pickup` and show_id separated by colon, e.g.: `pickup:1`
- referrer - referrer is an optional parameter which is used to generate
ready-to-use product URLs. URLs are returned in `sidestep_webstore_url` attribute.

```sh
curl --request GET \
  --url 'https://production.sidestepapp.com/api/artists/1/store/products?delivery_option_id=pickup:1&referrer=sidestep' \
  --header 'x-sidestep-app-token: your-sidestep-app-token'
```

#### Response

```json
{
  "products": [
    {
      "id": 4,
      "created_at": "2017-01-21T01:04:26.059Z",
      "updated_at": "2017-01-21T01:04:26.059Z",
      "description": "<!DOCTYPE html><html><body><p>Exotica Tee</p></body></html>",
      "order": null,
      "price": "55.0",
      "square_image": "https://production.sidestepapp.com/uploads/products/02a16363-7212-4767-9132-cdb9279ae2f8.jpg",
      "title": "Exotica Tee",
      "artist_id": 1,
      "tour_id": 1
    }
    ...
  ]
  ...
}
```

Product attributes:

Attribute             | Description
:-------------------- | :---------------------------------------------
id                    |
price                 | Product price in the currency used in the show
title                 |
square_image          | An URL to image. A size is 640x640 pixels.
sidestep_webstore_url | A ready-to-use URL of a product page on shopsidestepapp.com. Use it in `<a\>` tags.
