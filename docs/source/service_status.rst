
Querying service status
=======================

Reference
---------

API endpoint: https://xmr.to/api/service_status/

The service status endpoint supplies information about the service status and current parameters (price, order limits, etc).

Request
~~~~~~~

Issue a `GET` request to query current service status.

Response
~~~~~~~~

On success (`HTTP` code ``200``), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "lower_limit": <lower_order_limit_in_btc_as_float>, 
        "price": <price_of_1_btc_in_xmr_as_offered_by_service_as_float>, 
        "upper_limit": <upper_order_limit_in_btc_as_float>
    }

Fields should be self-explanatory.
On failure (when the service is not available), the `HTTP` return code is ``503``.

Example
-------

Request
~~~~~~~

.. sourcecode:: bash

    curl https://xmr.to/api/service_status/

Response
~~~~~~~~

.. sourcecode:: javascript

    {
        "price": 0.004011,
        "upper_limit": 1.0,
        "lower_limit": 0.001
    }



