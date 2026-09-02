# CookieOfficer CMP - Google Tag Manager template

Shows the CookieOfficer consent banner and passes the visitor decision to
Google Consent Mode v2. Until the visitor decides, a safe default state applies:
consent denied.

Learn more about CookieOfficer at [cookieofficer.com](https://cookieofficer.com).

## What the tag does

1. On **Consent Initialization - All Pages** it sets the default consent state
   for every Consent Mode v2 signal, globally or per region, and applies
   `ads_data_redaction` and `url_passthrough`.
2. It loads the consent banner for your site.
3. For a returning visitor it restores the saved decision right away, so signals
   do not sit on `denied` while the banner script is still loading.
4. When the visitor decides, the banner updates Consent Mode and pushes matching
   events to the data layer.

## Setup

1. Add the tag and paste your **Site ID**. You will find it in your CookieOfficer
   panel under Installation. Format: `co-XXXXXXXX`.
2. Set the trigger to **Consent Initialization - All Pages**.
3. Publish the container.

Everything else has a working default. Open **Consent Mode Settings** only if you
want a different default state for specific regions.

## Gating your tags

The rule differs for Google tags and for everything else. Mixing them up costs data.

**Google tags (GA4, Google Ads, Floodlight): leave Additional Consent unset.**
They already handle consent on their own: when consent is denied they switch to
cookieless measurement and still report the signals Google uses to model
conversions. Adding your own requirement turns them off completely, which removes
conversion modelling. Worse, a visitor who grants analytics but denies marketing
would get no measurement at all if the tag required `ad_storage`.

**Non-Google tags (Meta, HubSpot, LinkedIn, TikTok): set Additional Consent.**
They do not understand Consent Mode, so stop them yourself with the signal that
matches the category, usually `ad_storage` for marketing tags and
`analytics_storage` for analytics tags.

As an alternative you can trigger them on the data layer events the banner pushes
after a decision:

| Event | Fires when |
|---|---|
| `cookie_consent_update` | the consent state is settled or refreshed on this page |
| `cookie_consent_statistics` | analytics consent is granted |
| `cookie_consent_marketing` | marketing consent is granted |
| `cookie_consent_preferences` | preferences consent is granted |

## Fields

| Field | Description |
|---|---|
| **Site ID (data-co-id)** | Identifies your site. The only required field. |
| **Banner language** | Automatic follows the page, then the browser. Pick a language to override both, or use a container variable. |
| **Enable Google Consent Mode** | Turn off only when another tag in the container already manages consent signals. |
| **Default consent state** | Per region, with a blank Region meaning worldwide. An empty table denies everything, which is the safest option. |
| **Wait for update** | How long tags wait for the decision before measuring without cookies. 2000 ms recommended. |
| **Redact ads data** | While marketing consent is denied, Google Ads tags drop click identifiers and use a cookieless domain. |
| **Pass measurement parameters** | While consent is denied, measurement parameters travel in link URLs so the source of the visit is not lost. |

## Notes

Automatic script blocking is not available through Tag Manager, because the
container loads tags asynchronously. Under GTM, Consent Mode is the gate, which
is why non-Google tags need the Additional Consent setting above. Pasting the
CookieOfficer snippet directly into the page is the other option and blocks
scripts before they run.

## License

Apache License 2.0. See [LICENSE](LICENSE).
