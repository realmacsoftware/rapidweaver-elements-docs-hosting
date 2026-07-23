---
description: How to add cache control headers to your website
icon: gear-code
---

# Add Cache-Control Headers

## Configure Browser Caching with Cache-Control Headers

When someone visits your website, their browser downloads your site’s files, such as images, CSS stylesheets, JavaScript, and fonts. By default, the browser decides how long to keep these files before checking the server for newer versions.

By adding Cache-Control headers to your website, you can tell visitors’ browsers exactly how long different types of files should be cached. This can **significantly improve** page load times for returning visitors, **reduce** bandwidth usage, and **decrease** the number of requests your web server has to process.

The ideal cache duration depends on how frequently your website changes. A website that is updated several times a day will usually benefit from shorter cache durations, while a website that rarely changes can safely cache assets for much longer.

This guide provides three example configurations that you can add to your website’s `.htaccess` file:

* **Balanced** – Recommended for most websites. Provides good performance while ensuring updates are picked up in a reasonable amount of time.
* **Frequently Updated** – Uses shorter cache durations, making it better suited to websites where content changes regularly.
* **Aggressive** – Uses long cache durations to maximize performance for websites whose assets rarely change.

Choose the configuration that best matches how often you update your website. You can always switch to a different configuration later if your needs change.

***

{% hint style="warning" %}
Only use one of these configurations. **Do not** add all three examples to the same `.htaccess` file.
{% endhint %}

{% hint style="info" %}
Add the selected rules after any existing cache-header configuration. Multiple caching rules may generate duplicate or conflicting headers.
{% endhint %}

### Example 1 - Recommended (Balanced)

Suitable for most RapidWeaver Elements and RapidWeaver Classic websites.

```apache
# ------------------------------------------------------------
# Elements Hosting: Balanced browser caching
# ------------------------------------------------------------

<IfModule mod_expires.c>
    ExpiresActive On

    # Default for file types not explicitly listed below
    ExpiresDefault "access plus 1 hour"

    # HTML documents
    ExpiresByType text/html "access plus 1 hour"

    # Stylesheets
    ExpiresByType text/css "access plus 1 year"

    # JavaScript
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType text/javascript "access plus 1 year"

    # Fonts
    ExpiresByType font/woff2 "access plus 1 year"
    ExpiresByType font/woff "access plus 1 year"
    ExpiresByType font/ttf "access plus 1 year"
    ExpiresByType font/otf "access plus 1 year"
    ExpiresByType application/font-woff "access plus 1 year"
    ExpiresByType application/vnd.ms-fontobject "access plus 1 year"

    # Images
    ExpiresByType image/avif "access plus 30 days"
    ExpiresByType image/webp "access plus 30 days"
    ExpiresByType image/jpeg "access plus 30 days"
    ExpiresByType image/png "access plus 30 days"
    ExpiresByType image/gif "access plus 30 days"
    ExpiresByType image/svg+xml "access plus 30 days"
    ExpiresByType image/x-icon "access plus 30 days"

    # PDF documents
    ExpiresByType application/pdf "access plus 7 days"
</IfModule>

<IfModule mod_headers.c>
    # RapidWeaver normally cache-busts CSS and JavaScript when publishing.
    # Fonts are also suitable for long-lived immutable caching.
    <FilesMatch "\.(?:css|js|mjs|woff2?|ttf|otf|eot)$">
        Header merge Cache-Control "immutable"
    </FilesMatch>

    # Do not cache dynamic PHP responses.
    <FilesMatch "\.php$">
        # Remove headers generated in the normal response-header table.
        Header unset Expires
        Header unset Cache-Control
        Header unset Pragma

        # Remove equivalent headers from Apache's always header table.
        Header always unset Expires
        Header always unset Cache-Control
        Header always unset Pragma

        # Apply one authoritative no-cache policy.
        Header always set Cache-Control "no-store, no-cache, must-revalidate, max-age=0"
        Header always set Pragma "no-cache"
    </FilesMatch>
</IfModule>
```

#### Balanced durations

| Resource           | Origin cache lifetime |
| ------------------ | --------------------- |
| HTML               | 1 hour                |
| PHP                | Not cached            |
| CSS and JavaScript | 1 year, immutable     |
| Fonts              | 1 year, immutable     |
| Images             | 30 days               |
| PDFs               | 7 days                |

***

### Example 2 - Frequently Updated Websites

Suitable for websites whose pages and images are changed regularly.

```apache
# ------------------------------------------------------------
# Elements Hosting: Frequently updated website
# ------------------------------------------------------------

<IfModule mod_expires.c>
    ExpiresActive On

    # Default for file types not explicitly listed below
    ExpiresDefault "access plus 15 minutes"

    # HTML documents
    ExpiresByType text/html "access plus 15 minutes"

    # Stylesheets
    ExpiresByType text/css "access plus 1 year"

    # JavaScript
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType text/javascript "access plus 1 year"

    # Fonts
    ExpiresByType font/woff2 "access plus 1 year"
    ExpiresByType font/woff "access plus 1 year"
    ExpiresByType font/ttf "access plus 1 year"
    ExpiresByType font/otf "access plus 1 year"
    ExpiresByType application/font-woff "access plus 1 year"
    ExpiresByType application/vnd.ms-fontobject "access plus 1 year"

    # Images
    ExpiresByType image/avif "access plus 7 days"
    ExpiresByType image/webp "access plus 7 days"
    ExpiresByType image/jpeg "access plus 7 days"
    ExpiresByType image/png "access plus 7 days"
    ExpiresByType image/gif "access plus 7 days"
    ExpiresByType image/svg+xml "access plus 7 days"
    ExpiresByType image/x-icon "access plus 7 days"

    # PDF documents
    ExpiresByType application/pdf "access plus 1 day"
</IfModule>

<IfModule mod_headers.c>
    # RapidWeaver normally cache-busts CSS and JavaScript when publishing.
    # Fonts are also suitable for long-lived immutable caching.
    <FilesMatch "\.(?:css|js|mjs|woff2?|ttf|otf|eot)$">
        Header merge Cache-Control "immutable"
    </FilesMatch>

    # Do not cache dynamic PHP responses.
    <FilesMatch "\.php$">
        # Remove headers generated in the normal response-header table.
        Header unset Expires
        Header unset Cache-Control
        Header unset Pragma

        # Remove equivalent headers from Apache's always header table.
        Header always unset Expires
        Header always unset Cache-Control
        Header always unset Pragma

        # Apply one authoritative no-cache policy.
        Header always set Cache-Control "no-store, no-cache, must-revalidate, max-age=0"
        Header always set Pragma "no-cache"
    </FilesMatch>
</IfModule>
```

#### Frequently updated durations

| Resource           | Origin cache lifetime |
| ------------------ | --------------------- |
| HTML               | 15 minutes            |
| PHP                | Not cached            |
| CSS and JavaScript | 1 year, immutable     |
| Fonts              | 1 year, immutable     |
| Images             | 7 days                |
| PDFs               | 1 day                 |

***

### Example 3 - Aggressive Caching

Suitable for relatively static websites where minimal changes are made. For users who prefer maximum repeat-visit performance over rapid propagation of content changes.

```apache
# ------------------------------------------------------------
# Elements Hosting: Aggressive browser caching
# ------------------------------------------------------------

<IfModule mod_expires.c>
    ExpiresActive On

    # Default for file types not explicitly listed below
    ExpiresDefault "access plus 1 day"

    # HTML documents
    ExpiresByType text/html "access plus 1 day"

    # Stylesheets
    ExpiresByType text/css "access plus 1 year"

    # JavaScript
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType text/javascript "access plus 1 year"

    # Fonts
    ExpiresByType font/woff2 "access plus 1 year"
    ExpiresByType font/woff "access plus 1 year"
    ExpiresByType font/ttf "access plus 1 year"
    ExpiresByType font/otf "access plus 1 year"
    ExpiresByType application/font-woff "access plus 1 year"
    ExpiresByType application/vnd.ms-fontobject "access plus 1 year"

    # Images
    ExpiresByType image/avif "access plus 6 months"
    ExpiresByType image/webp "access plus 6 months"
    ExpiresByType image/jpeg "access plus 6 months"
    ExpiresByType image/png "access plus 6 months"
    ExpiresByType image/gif "access plus 6 months"
    ExpiresByType image/svg+xml "access plus 6 months"
    ExpiresByType image/x-icon "access plus 6 months"

    # PDF documents
    ExpiresByType application/pdf "access plus 30 days"
</IfModule>

<IfModule mod_headers.c>
    # RapidWeaver normally cache-busts CSS and JavaScript when publishing.
    # Fonts are also suitable for long-lived immutable caching.
    <FilesMatch "\.(?:css|js|mjs|woff2?|ttf|otf|eot)$">
        Header merge Cache-Control "immutable"
    </FilesMatch>

    # Do not cache dynamic PHP responses.
    <FilesMatch "\.php$">
        # Remove headers generated in the normal response-header table.
        Header unset Expires
        Header unset Cache-Control
        Header unset Pragma

        # Remove equivalent headers from Apache's always header table.
        Header always unset Expires
        Header always unset Cache-Control
        Header always unset Pragma

        # Apply one authoritative no-cache policy.
        Header always set Cache-Control "no-store, no-cache, must-revalidate, max-age=0"
        Header always set Pragma "no-cache"
    </FilesMatch>
</IfModule>
```

#### Aggressive caching durations

| Resource           | Origin cache lifetime |
| ------------------ | --------------------- |
| HTML               | 24 hours              |
| PHP                | Not cached            |
| CSS and JavaScript | 1 year, immutable     |
| Fonts              | 1 year, immutable     |
| Images             | 6 months              |
| PDFs               | 30 days               |

***

## Cloudflare Browser Cache TTL

If your website uses Cloudflare, its **Browser Cache TTL** setting can affect the cache durations you configure in your `.htaccess` file.

By default, websites using **Elements Hosting Managed DNS** are configured with a **4-hour Browser Cache TTL**. This means Cloudflare may increase any browser cache duration shorter than four hours to four hours, even if your `.htaccess` file specifies a shorter value. Longer cache durations are not affected.

If you want the cache durations in the examples on this page to be used exactly as written, change your **Browser Cache TTL** setting to **Respect Existing Headers** in your Cloudflare dashboard.

<figure><img src="../../.gitbook/assets/cf-browser-cache-ttl-setting@2x.webp" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
**Important:** Only select **Respect Existing Headers** after adding cache-control headers to your website. Without cache-control headers, Cloudflare will no longer apply its own browser cache duration, which may result in some files not being cached by visitors’ browsers.
{% endhint %}
