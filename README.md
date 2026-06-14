[Duckduckgo Search Scraper](https://apify.com/runtime/duckduckgo-search-scraper?fpr=data)

A web scraping Actor built with Apify SDK and Crawlee that extracts search results from DuckDuckGo in multiple search modes. Supports batch queries, pagination, and optional email discovery from snippets.

## Features

- **Multiple search modes**: Default (web), images, news, videos
- **Batch queries**: Run many search queries in one run via a list (one per line; empty rows are removed automatically)
- **Single or list**: Use one search query or an array; comma-separated or newline-separated text is supported
- **Query options**: Remove duplicate queries, optional cap on number of queries (max queries)
- **Auto pagination**: Follow “More results” to collect up to `maxResults` across pages
- **Email discovery**: Optional extraction of email addresses from result titles and snippets, with optional domain filter and query suffix for contact/press/legal discovery
- **Structured output**: Each result includes title, URL, snippet, position, type, and page; optional `emails` array when email discovery is enabled

## Search Modes

| Mode | Description |
| --- | --- |
| Default | Standard web search |
| Images | Image search results |
| News | News articles |
| Videos | Video search results |

## Input

| Parameter | Required | Description |
| --- | --- | --- |
| **searchQuery** | Yes* | Single search term or phrase. Used when **Search Queries (array)** is empty. Comma-separated values are split into multiple queries. |
| **searchQueries** | No | List of search terms (e.g. from Bulk edit, one per line). Overrides **searchQuery** when non-empty. Empty rows are removed. |
| **removeDuplicateQueries** | No | Remove duplicate entries in the query list (default: true). |
| **maxQueries** | No | Max number of queries to run from the list; 0 = no limit (default: 0). |
| **searchMode** | Yes | `default`, `images`, `news`, or `videos`. |
| **maxResults** | No | Max results to extract per run (default: 500, max: 1000). |
| **maxPages** | No | Max result pages to crawl (default: 25, max: 100). |
| **autoPagination** | No | Click “More results” to get more pages (default: true). |
| **extractEmails** | No | Parse titles and snippets for email addresses and add an `emails` array to each result (default: false). |
| **emailDomainFilter** | No | When extracting emails, keep only addresses containing this domain (e.g. `20minutes.fr`). Empty = all emails. |
| **emailSearchSuffix** | No | Suffix appended to every search query (e.g. ` contact email`, ` "nous contacter"`). |

* Either **searchQuery** or **searchQueries** must provide at least one query; **searchQuery** has a default.

## Output

Results are stored in the default dataset. Each item looks like:

```
{
  "title": "Result title",
  "url": "https://example.com/page",
  "snippet": "Description or snippet from the result",
  "position": 1,
  "type": "default",
  "page": 1
}
```

When **Extract emails from results** is enabled, each item can include:

```
{
  "title": "...",
  "url": "...",
  "snippet": "...",
  "position": 1,
  "type": "default",
  "page": 1,
  "emails": ["contact@example.com", "press@example.com"]
}
```

- **position**: Rank on the current page (1-based).
- **page**: Page number where the result was found.
- **type**: `default`, `images`, `news`, `videos`, or `image`.
- **emails**: Present only when email extraction is on; deduplicated per result; filtered by **emailDomainFilter** if set.

## Usage Examples

### Single search

```
{
  "searchQuery": "artificial intelligence",
  "searchMode": "default",
  "maxResults": 50
}
```

### Multiple queries (array)

```
{
  "searchQueries": [
    "site:example.com contact",
    "site:example.com \"nous contacter\" email"
  ],
  "searchMode": "default",
  "maxResults": 100
}
```

### Email discovery (contact/press)

```
{
  "searchQueries": [
    "site:20minutes.fr contact email",
    "site:20minutes.fr \"mentions légales\" email",
    "site:20minutes.fr \"presse\" email"
  ],
  "searchMode": "default",
  "extractEmails": true,
  "emailDomainFilter": "20minutes.fr",
  "emailSearchSuffix": " email",
  "maxResults": 100
}
```

### Limit number of queries (test run)

```
{
  "searchQueries": ["query1", "query2", "query3", "..."],
  "searchMode": "default",
  "maxQueries": 5,
  "maxResults": 20
}
```

### Multi-page with pagination

```
{
  "searchQuery": "web development tutorials",
  "searchMode": "default",
  "maxResults": 100,
  "maxPages": 5,
  "autoPagination": true
}
```

### Image search

```
{
  "searchQuery": "nature photography",
  "searchMode": "images",
  "maxResults": 30
}
```

## Legal

This Actor is for educational and research use. You are responsible for:

- Complying with DuckDuckGo’s terms of service
- Respecting robots.txt and site terms
- Following applicable laws
- Using the tool in a responsible and ethical way

The developers are not responsible for misuse or violations of terms of service.