Creating a new order
====================

Reference
---------

API endpoint: https://xmr.to/api/create_order/

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

On failure, the `HTTP` return code is:

 - ``400`` malformed request (check your input parameters!)
 - ``503`` service not available (XMR.TO is currently down)


Example
-------

In this example, we create an order for donating 0.1 BTC to the Monero developers (using Bitcoin, ironically).

Request
~~~~~~~

.. sourcecode:: bash

    curl --data '{"btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb", \
        "btc_amount": 0.1}' -H "Content-Type: application/json" https://xmr.to/api/create_order/

Response
~~~~~~~~

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": 0.1,
        "btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb",
        "uuid": "xmrto-XCZEsu"
    }

