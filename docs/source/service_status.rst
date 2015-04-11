
Querying service status
=======================

Reference
---------

API endpoint: https://xmr.to/api/service_status_query/

The service status endpoint supplies information about whether new orders can be created.
In this case, this endpoint provides the current parameters (price, order limits, etc) for newly created orders.

.. note::
    It is possible to query the status of existing orders even if the service status
    endpoint reports `not available`.

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

On failure, one of the following errors is returned:

=========   ===================     =================================    ================
HTTP code   XMR.TO error code       Error message/reason                 Solution
=========   ===================     =================================    ================
``503``     ``XMRTO-ERROR-001``     internal services not available      try again later
``503``     ``XMRTO-ERROR-007``     third party service not available    try again later
``503``     ``XMRTO-ERROR-008``     insufficient funds available         try again later
=========   ===================     =================================    ================


Example
-------

Request
~~~~~~~

.. sourcecode:: bash

    curl https://xmr.to/api/service_status_query/

Response
~~~~~~~~

.. sourcecode:: javascript

    {
        "price": 0.004011,
        "upper_limit": 1.0,
        "lower_limit": 0.001
    }



