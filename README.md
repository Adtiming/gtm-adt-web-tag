# AdTiming Web Tag for Google Tag Manager

Use this template to load the AdTiming Web Tag SDK and send conversion events from a web container.

## Set up the SDK

1. Add the **AdTiming Web Tag – Events** template to your web container.
2. Create one tag from the template. Set **Mode** to **Setup** and enter the **Partner ID** assigned by AdTiming.
3. For a site where the SDK can start at page initialization, select **Initialization – All Pages**. If your site requires consent before tracking, configure the setup and event tags to fire only after your consent requirements are met.

The SDK sends a page view (event 9) after initialization. Use one SDK setup per page.

## Send conversion events

Create a tag from the same template for each event you want to report. Set **Mode** to **Event**, choose the matching **Event type**, and attach a **Custom Event** trigger whose name matches the `event` value below.

| `event` | AdTiming event ID | Data layer fields |
| --- | ---: | --- |
| `view_item` | 4 | `ecommerce.items` |
| `add_to_cart` | 5 | `ecommerce.items` |
| `view_cart` | 6 | `ecommerce.items` |
| `begin_checkout` | 7 | `ecommerce.items` |
| `purchase` | 8 | `ecommerce.items`, `ecommerce.transaction_id`, `ecommerce.value` |
| `remove_from_cart` | 17 | `ecommerce.items` |
| `login` | 13 | Optional `cuid` and `method` |
| `sign_up` | 14 | Optional `cuid` and `method` |
| `kyc` | 15 | Optional `cuid` |
| `deposit` | 16 | Optional `cuid` |

Each ecommerce event needs at least one item with an `item_id`. The template accepts optional `price` and `quantity` and defaults a missing or invalid quantity to 1. Before each ecommerce event, clear the previous ecommerce object so GTM does not reuse its values.

```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({ ecommerce: null });
window.dataLayer.push({
  event: 'purchase',
  ecommerce: {
    transaction_id: 'ORDER-10001',
    value: 99.99,
    items: [{ item_id: 'SKU-001', price: 99.99, quantity: 1 }]
  }
});
```

For account events, pass `cuid` and `method` at the top level of the same data layer push. Use an opaque customer ID for `cuid`. When an optional field read by the event has no value, set it to `null` in that push to clear any prior value in GTM.

```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({ event: 'login', cuid: 'customer-42', method: 'sso' });
```

## Verify your setup

In GTM Preview, check that the Setup tag fires and that each Event tag fires on its matching custom event. In your browser's Network panel, check the `POST https://rt.adtiming.com/event` requests for the expected event IDs. Where present, `cuid` is a top-level request field and `method` is sent as `data.method`.
