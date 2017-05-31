### Periscope API Docs: https://doc.periscopedata.com/docv2/embed-api

### Periscope Embed Options: https://doc.periscopedata.com/docv2/embed-api-options

1. Build a JSON payload containing a dashboard ID (from the URL to the dashboard) and a timestamp from the past that force a refresh. You can always use the below `data_ts` value as it corresponds to a date in the past.
```
{
    "dashboard": 136556,
    "data_ts": 1446069112
}
```
2. URL encode the payload here `https://codebeautify.org/url-encode-string`
3. Take the URL encoded string (`%7B%0A%20%20%20%20%22dashboard%22%3A%20136556%2C%0A%20%20%20%20%22data_ts%22%3A%201446069112%0A%7D`) to `http://www.freeformatter.com/hmac-generator.html#ad-output` and encode it using the API key from periscope with the SHA256 setting. The output will look something like `6ea0be3d2eababee705c1844c8571ddb9872ec4e0e499ecbf0e13f3885b1f22e`
4. Build the URL with these components: `https://www.periscopedata.com/api/embedded_dashboard?data=<url_encoded_payload>&signature=<sha256_string>`

Example: `https://www.periscopedata.com/api/embedded_dashboard?data=%7B%22dashboard%22%3A7863%2C%22embed%22%3A%22v2%22%2C%22filters%22%3A%5B%7B%22name%22%3A%22Filter1%22%2C%22value%22%3A%22value1%22%7D%2C%7B%22name%22%3A%22Filter2%22%2C%22value%22%3A%221234%22%7D%5D%7D&signature=adcb671e8e24572464c31e8f9ffc5f638ab302a0b673f72554d3cff96a692740`
