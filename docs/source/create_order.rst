Creating a new order
====================

Reference
---------

API endpoint: https://xmr.to/api/order_create/

The order creation endpoint allows to create a new order at the current price.
The user has to supply a bitcoin destination address and amount to create the order.

Request
~~~~~~~

Issue a `POST` request to create a new order supplying the following parameters:

.. sourcecode:: javascript

    {        
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": "<requested_destination_address_as_string>"
    }

.. note::
    Make sure that ``btc_amount`` amount is inside the possible limits for an order.
    These limits can be queried using the ``service_status_query`` endpoint.


Response
~~~~~~~~

On success (`HTTP` code ``200``), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": "<requested_destination_address_as_string>",
        "uuid": "<unique_order_identifier_as_12_character_string>"
    }

The field ``state`` reflects the state of an order. If ``state`` is ``TO_BE_CREATED`` in the
response, the order has been registered for creation and you can use the order ``uuid`` 
to start querying the order's status. All other fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     ================================    ================
HTTP code   XMR.TO error code       Error message/reason                Solution
=========   ===================     ================================    ================
``503``     ``XMRTO-ERROR-001``     internal services not available     try again later
``400``     ``XMRTO-ERROR-002``     malformed bitcoin address           check address validity
``400``     ``XMRTO-ERROR-003``     invalid bitcoin amount              check amount data type
``503``     ``XMRTO-ERROR-004``     bitcoin amount out of bounds        check min and max amount
``400``     ``XMRTO-ERROR-005``     unexpected validation error         contact support
=========   ===================     ================================    ================



Example
-------

In this example, we create an order for donating 0.1 BTC to the Monero developers (using Bitcoin, ironically).

Request
~~~~~~~

.. sourcecode:: bash

    curl --data '{"btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb", \
        "btc_amount": 0.1}' -H "Content-Type: application/json" https://xmr.to/api/order_create/

.. hint::
    Remember to set the `HTTP` Content-Type to ``application/json``!


Response
~~~~~~~~

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": 0.1,
        "btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb",
        "uuid": "xmrto-XCZEsu"
    }

