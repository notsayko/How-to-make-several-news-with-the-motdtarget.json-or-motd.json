> [!WARNING]
> Back up your original MOTD response before editing it. Invalid JSON or missing required fields can stop the news response from working.

> [!TIP]
> Need help? Join the [Discord server](https://discord.gg/MCDGDH9nbR) and share your setup or error details. If this guide helps you, please **star the repository** — it really helps support more guides like this.

---

# Adding Multiple News to OGFN

This guide explains how to show more than one news card in OGFN by editing the MOTD collection returned by your backend.

## How it works

The MOTD response is a JSON collection. The `contentItems` array contains the news cards that the game can display.

- With **one** news card, `contentItems` contains one object.
- With **two** news cards, it contains two objects.
- To add more cards, add another `content-item` object to the same array.

Keep the collection-level fields (`contentType`, `contentId`, and the collection `tcId`) as they are unless your backend specifically requires different values.

## 1. Find the MOTD response

Find the backend route or JSON file that serves the MOTD/news collection. Back up the original response before editing it.

The response should have this general structure:

```json
{
  "contentType": "collection",
  "contentId": "motd-default-collection",
  "tcId": "634e8e85-e2fc-4c68-bb10-93604cf6605f",
  "contentItems": [
    {
      "...": "first news item"
    },
    {
      "...": "second news item"
    }
  ]
}
```

The example above is only a structural illustration: each item must be a complete news object, not the literal placeholder shown here.

## 2. Add a second news item

If your current response has one object inside `contentItems`, keep that object and add a second complete object after it. Separate the objects with a comma.

Here is a minimal example with two independent news cards:

```json
{
  "contentType": "collection",
  "contentId": "motd-default-collection",
  "tcId": "634e8e85-e2fc-4c68-bb10-93604cf6605f",
  "contentItems": [
    {
      "contentType": "content-item",
      "contentId": "news-item-001",
      "tcId": "news-tc-001",
      "contentFields": {
        "body": {
          "en": "The first news item description.",
          "fr": "Description de la première actualité."
        },
        "entryType": "Website",
        "image": [
          {
            "width": 1920,
            "height": 1080,
            "url": "https://example.com/news-one.jpg"
          }
        ],
        "tabTitleOverride": "First News",
        "tileImage": [
          {
            "width": 1024,
            "height": 512,
            "url": "https://example.com/news-one-tile.jpg"
          }
        ],
        "title": {
          "en": "First News",
          "fr": "Première actualité"
        },
        "videoAutoplay": false,
        "videoLoop": false,
        "videoMute": false,
        "videoStreamingEnabled": false,
        "websiteButtonText": "Learn more",
        "websiteURL": "https://example.com/news-one"
      },
      "contentHash": "news-content-hash-001",
      "contentSchemaName": "MotdWebsiteNewsWithVideo"
    },
    {
      "contentType": "content-item",
      "contentId": "news-item-002",
      "tcId": "news-tc-002",
      "contentFields": {
        "body": {
          "en": "The second news item description.",
          "fr": "Description de la deuxième actualité."
        },
        "entryType": "Website",
        "image": [
          {
            "width": 1920,
            "height": 1080,
            "url": "https://example.com/news-two.jpg"
          }
        ],
        "tabTitleOverride": "Second News",
        "tileImage": [
          {
            "width": 1024,
            "height": 512,
            "url": "https://example.com/news-two-tile.jpg"
          }
        ],
        "title": {
          "en": "Second News",
          "fr": "Deuxième actualité"
        },
        "videoAutoplay": false,
        "videoLoop": false,
        "videoMute": false,
        "videoStreamingEnabled": false,
        "websiteButtonText": "View details",
        "websiteURL": "https://example.com/news-two"
      },
      "contentHash": "news-content-hash-002",
      "contentSchemaName": "MotdWebsiteNewsWithVideo"
    }
  ]
}
```

**Important:** `example.com` URLs and the sample IDs/hashes above are placeholders. Replace them with values suitable for your backend. Use unique `contentId` and `tcId` values for each item if your backend/client expects item identifiers to be unique. If your implementation validates `contentHash`, generate the value the way your backend expects rather than assuming any arbitrary string will work.

## 3. What to change for each news card

Each object in `contentItems` represents one card. Set these fields for every item:

| Field | Purpose |
| --- | --- |
| `contentId` | Identifier for the news item. Keep it distinct between items if required by your backend. |
| `tcId` | Item tracking/content identifier. Follow the format expected by your backend. |
| `contentFields.title` | News headline, with one value per language. |
| `contentFields.body` | News description, with one value per language. |
| `contentFields.image` | Main image shown for the news item. |
| `contentFields.tileImage` | Thumbnail/tile image for the item. |
| `contentFields.tabTitleOverride` | Optional title used by the news tab/UI. |
| `contentFields.websiteButtonText` | Text shown on the action button. |
| `contentFields.websiteURL` | Destination opened by that button. |
| `contentSchemaName` | Keep the schema supported by your client, such as `MotdWebsiteNewsWithVideo`. |

Use image URLs that are publicly reachable by the game client. Make sure the images match the dimensions/aspect ratios expected by your client.

## 4. Adding a third or fourth news item

Repeat the same process: add another complete object inside `contentItems`, separated by commas. Do not put the new object outside the array, and do not nest another `contentItems` array inside a news item.

The structure should look like this:

```json
"contentItems": [
  { "...": "first complete news item" },
  { "...": "second complete news item" },
  { "...": "third complete news item" }
]
```

This is a schematic example, not valid final JSON as written; replace each placeholder with a complete object.

## 5. Validate and test

Before restarting or testing the backend:

1. Check the JSON with a JSON validator. Trailing commas and missing commas will break parsing.
2. Confirm each news object has all required fields and the expected schema.
3. Confirm each image and `websiteURL` loads correctly.
4. Restart/reload the backend if your setup caches the MOTD response.
5. Launch OGFN and check whether the additional cards appear.

## Troubleshooting

- **Only one card appears:** confirm the response actually contains multiple objects in `contentItems`, then check whether your OGFN client build supports multiple MOTD items.
- **The news section is empty:** inspect the backend logs and validate the JSON syntax and required fields.
- **A card has a blank image:** check the image URL, file format, and accessibility from the game client.
- **The button opens the wrong page:** verify the `websiteURL` for that specific item.
- **The second item is ignored:** check for duplicate IDs, invalid hashes, or backend/client validation requirements.

The key change is the `contentItems` array: each news card needs its own complete object. Whether every card is displayed depends on the OGFN client build and the backend's MOTD handling.
