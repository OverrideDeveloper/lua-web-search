# lua-web-search

A Lua-based engine for web searches and offline data.

## MVP

The first version provides a small provider abstraction and a DuckDuckGo HTML provider.

```lua
local WebSearchEngine = require("web_search_engine")
local DuckDuckGo = require("providers.duckduckgo")

local search = WebSearchEngine.new({ max_results = 5 })
search:add_provider(DuckDuckGo.new())

search:search("famous historical events on September 20", function(result)
    for _, item in ipairs(result.results) do
        print(item.title, item.url)
    end
end)
```

## Result model

The engine distinguishes a provider outcome from the final result set.

```lua
{
    status = "success",
    query = "...",
    results = {
        {
            title = "...",
            url = "...",
            snippet = "...",
            provider = "duckduckgo",
        },
    },
    retrieval = {
        {
            provider = "duckduckgo",
            status = "success",
            result_count = 5,
        },
    },
}
```

Provider failures are not silently converted into "no results". The MVP recognizes network errors, HTTP errors, automated-access challenges, zero parsed results, and successful results.

## Provider contract

A provider has:
- `name`
- `search(query, max_results, callback)`

The callback receives:
- `status`
- `results`
- optional `message`

This keeps provider-specific HTTP and parsing details outside the engine.

## Current provider

- DuckDuckGo HTML search

The DuckDuckGo provider uses the HTML endpoint and detects the automated-access challenge observed during development. It does not attempt to bypass that challenge.

## Offline data

Offline providers are intentionally part of the architecture but are not implemented yet. The intended next provider is a local Wikipedia corpus/search endpoint.

## Runtime

The MVP is written for the Lua/Luvit-style asynchronous HTTP model used by AliceWebAI.

Runtime validation could not be performed in this environment because a Lua/Luvit runtime is not available here.
