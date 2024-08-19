<!-- ---
margin-top: 1cm
margin-bottom: 1cm
margin-left: 1cm
margin-right: 1cm
--- -->

# Exchange data api
- Base url: https://api.exchange-data.carmine.finance


### Endpoints
#### */v1/liveness*
  - Returns "Live"

#### */v1/errors*
- Given `start` and `end` timestamp, returns list of errors encountered when fetching the data, for all available symbols

<center>

| Param | Type  | Description|
|--------|-------|------------|
|`start` | `int` | UNIX timestamp |
|`end`   | `int` | UNIX timestamp, not inclusive|

</center>

- **Returns**
    - List of JSONs with following fields:
      - `timestamp`: When did the error occur
      - `exchange`: Exchange 
      - `symbol`: Our symbol representation
      - `exchange_symbol`: How the symbol is represented in exchange API
      - `msg`: Error message received by the exchange API

    - Example:
        ```
        [{
            'timestamp': '1723883987',
            'exchange': 'GATE',
            'symbol': 'SOL-USDT',
            'exchange_symbol': 'SOL_USDT',
            'msg': 'Internal server error'
        }]
        ```

    - Note that endpoint returns daily data only, meaning that for `start` and `end`, data for every day starting from `start` till the `end` (not inclusive) will be returned

- **Limits**:
  - Max of 5 days can be fetched (`end` - `start` <= 86400 * 5)

- **Headers**:
  - `X-API-Key` header needs to be provided with valid API key

- **Fetch example**:

    ```python
    import requests
    import time

    base_url = "https://api.exchange-data.carmine.finance"
    endpoint = "/v1/errors"

    end = int(time.time())
    start =  end - 86_400

    headers = {
        "X-API-Key" : "<INSERT API KEY HERE>"
    }

    request_url = (
        base_url +
        endpoint + 
        f"?start={start}&end={end}"
    )

    response = requests.get(request_url, headers=headers)
    ```


#### */v1/orderbooks*
- Given `start` and `end` timestamp and `symbol` returns list of orderbook snapshots for given symbol, across all monitored venues
- Allowed symbols: SOL-USDT, SOL-USDC, SOL-USD, SOL-USDT-PERP, SOL-USDC-PERP

<center>

| Param | Type  | Description|
|--------|-------|------------|
|`start` | `int` | UNIX timestamp |
|`end`   | `int` | UNIX timestamp, not inclusive|
|`symbol`| `str` | Symbol identifier|

</center>

<!-- ['timestamp', 'exchange', 'symbol', 'exchange_symbol', 'bids', 'asks'] -->
- **Returns**
    - List of JSONs with following fields:
      - `timestamp`: When did the error occur
      - `exchange`: Exchange 
      - `symbol`: Our symbol representation
      - `exchange_symbol`: How the symbol is represented in exchange API
      - `bids`: List of bids which are two-sized lists [price, amount]
      - `asks`: List of asks which are two-sized lists [price, amount]

    - Example:
        ```
        {
            'timestamp': '1724008227',
            'exchange': 'BINANCE',
            'symbol': 'SOL-USDT',
            'exchange_symbol': 'SOLUSDT',
            'bids': [
                ['145.66000000', '104.86900000'],
                ['145.65000000', '99.47700000'],
                ['145.64000000', '279.89900000'],
                ['145.63000000', '168.73900000'],
                ...
            ],
            'asks': [
                ['145.67000000', '263.95200000'],
                ['145.68000000', '258.06600000'],
                ['145.69000000', '90.96400000'],
                ['145.70000000', '408.18300000'],
                ...
            ]
        }
        ```

    - Note that endpoint returns daily data only, meaning that for `start` and `end`, data for every day starting from `start` till the `end` (not inclusive) will be returned
    - For every orderbook snapshot, 100 levels is returned

- **Limits**:
  - Max of 5 days can be fetched (`end` - `start` <= 86400 * 5)

- **Headers**:
  - `X-API-Key` header needs to be provided with valid API key

- **Fetch example**:
    ```python
    import requests
    import time

    base_url = "https://api.exchange-data.carmine.finance"
    endpoint = "/v1/orderbooks"

    end = int(time.time())
    start = end - 86_400
    symbol = 'SOL-USDT'

    headers = {
        "X-API-Key" : "<INSERT API KEY HERE>"
    }

    request_url = (
        base_url +
        endpoint + 
        f"?start={start}&end={end}&symbol={symbol}"
    )

    response = requests.get(request_url, headers=headers)
    ```
