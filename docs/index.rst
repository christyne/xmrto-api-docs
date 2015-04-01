.. XMR.TO API documentation master file, created by
   sphinx-quickstart on Wed Apr 1 16:43:52 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the XMR.TO API documentation!
=======================================================

This is the API documentation for XMR.TO - https://xmr.to

Introduction
------------

XMR.TO is a service that allows users to pay any bitcoin address
anonymously using Monero. 
Monero is a secure, private, untraceable currency that is open-source
and freely available to all. https://getmonero.org/
XMR.TO converts moneroj (XMR) provided by the user to bitcoins
(BTC), which are sent to a given bitcoin address.

XMR.TO provides an API that enables developers to use the service
in programs, apps, scripts etc. This API therefore allows developers
to integrate an option to pay any bitcoin address in their product,
for example, in a monero wallet service. 
The API is a REST-like API using `JSON` as dataformat. It is available anonymously
and does not require identification or authentification.
This document describes this API and gives examples of its usage.


Overview
--------

In order to use XMR.TO, a user must first create an order over
a certain amount of bitcoins (BTC). Once the order has been created,
XMR.TO provides the user with the details for the payment in moneroj (XMR).
Once the user has fully paid using Monero, XMR.TO will create a bitcoin
transaction over the given amount to the given bitcoin address.

When using the API, the general flow of events is similar:

 - get service status to see if XMR.TO is available and to fetch current parameters (price, order limits, etc)
 - create a new order by supplying bitcoin destination address and mount
 - check order status to get payment information
 - pay order
 - continue to repeatedly check order status for processing progress



Querying service status
-----------------------

API endpoint: https://xmr.to/api/service_status/

The service status endpoint supplies information about the service status and current parameters (price, order limits, etc).


Request
"""""""

Issue a `GET` request to query current service status.


Response
""""""""

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
^^^^^^^

Request:

.. sourcecode:: bash

    curl https://xmr.to/api/service_status/

Response:

.. sourcecode:: javascript

    {
        "price": 0.004011,
        "upper_limit": 1.0,
        "lower_limit": 0.001
    }



Creating a new order
--------------------

API endpoint: https://xmr.to/api/create_order/

The order creation endpoint allows to create a new order at the current price.
The user has to supply a bitcoin destination address and amount to create the order.


Request
^^^^^^^

Issue a `POST` request to create a new order supplying the following parameters:

.. sourcecode:: javascript

    {        
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": "<requested_destination_address_as_string>"
    }


Response
^^^^^^^^

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
^^^^^^^

In this example, we create an order for donating 0.1 BTC to the Monero developers (using Bitcoin, ironically):

.. sourcecode:: bash

    curl --data '{"btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb", \
        "btc_amount": 0.1}' -H "Content-Type: application/json" https://xmr.to/api/create_order/

Response:

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": 0.1,
        "btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb",
        "uuid": "xmrto-XCZEsu"
    }




Querying order status
---------------------

API endpoint: https://xmr.to/api/order_status/

The order status endpoint allows users to query the status of an order, thereby obtaining payment details and order processing progress.


Request
^^^^^^^

Issue a `POST` request to query the status of a given order.
You have to supply the order's ``uuid`` in the request:

.. sourcecode:: javascript

    {        
        "uuid": "<unique_order_identifier_as_12_character_string>",
    }


Response
^^^^^^^^

On success (`HTTP` code ``200``), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "state": "<order_state_as_string>",
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": "<requested_destination_address_as_string>",
        "uuid": "<unique_order_identifier_as_12_character_string>"
        "btc_num_confirmations": <btc_num_confirmations_as_integer>, 
        "btc_num_confirmations_before_purge": <btc_num_confirmations_before_purge_as_integer>, 
        "btc_transaction_id": "<btc_transaction_id_as_string>", 
        "created_at": "<timestamp_as_string>", 
        "expires_at": "<timestamp_as_string>", 
        "seconds_till_timeout": <seconds_till_timeout_as_integer>, 
        "xmr_amount_remaining": <amount_in_xmr_that_the_user_must_still_send_as_float>, 
        "xmr_num_confirmations_remaining": <num_xmr_confirmations_remaining_before_bitcoins_will_be_sent_as_integer>, 
        "xmr_price_btc": <price_of_1_btc_in_xmr_as_offered_by_service_as_float>, 
        "xmr_receiving_address": "xmr_address_user_needs_to_send_funds_to_as_string", 
        "xmr_required_amount": <xmr_amount_user_needs_to_send_as_float>, 
        "xmr_required_payment_id": "xmr_payment_id_user_needs_to_include_when_paying_as_string"
    }

Presence of some of these fields depend on ``state``, which can take the following values:

 - ``TO_BE_CREATED`` order creation pending
 - ``UNPAID`` waiting for XMR payment by user
 - ``UNDERPAID`` order partially paid
 - ``PAID_UNCONFIRMED`` order paid, waiting for confirmation
 - ``PAID`` order paid and confirmed
 - ``BTC_SENT`` BTC payment sent
 - ``TIMED_OUT`` order timed out before payment was complete
 - ``NOT_FOUND`` order wasn't found in system (never existed or was purged)

All other fields should be self-explanatory.

On failure, the `HTTP` return code is:

 - ``400`` malformed request (check your input parameters!)
 - ``503`` service not available (XMR.TO is currently down)


Example
^^^^^^^

Continuing from our previous example, we can query the order by supplying the order's unique identifier ``uuid`` as follows:

.. sourcecode:: bash

    curl --data '{"uuid": "xmrto-VkT2yM"}' -H "Content-Type: application/json" \
        https://xmr.to/api/order_status/

The response gives the current status of the order:

.. sourcecode:: javascript

    {
        "xmr_price_btc": 0.003963,
        "uuid": "xmrto-XCZEsu",
        "state_str": "UNPAID",
        "btc_amount": 0.1,
        "btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb",
        "xmr_required_amount": 25.233409,
        "xmr_receiving_address": "44TVPcCSHebEQp4LnapPkhb2pondb2Ed7GJJLc6TkKwtSyumUnQ6QzkCCkojZycH2MRfLcujCM7QR1gdnRULRraV4UpB5n4",
        "xmr_required_payment_id":"223907873a29a00e3a5ff563c3b65f278ab6eb0cba623428ca3d9aaa54ea7bbb",
        "created_at": "2015-04-01T16:03:27Z",
        "expires_at": "2015-04-01T16:08:27Z",
        "seconds_till_timeout": 224,
        "xmr_amount_remaining": 25.233409,
        "xmr_num_confirmations_remaining": -1,
        "btc_num_confirmations_before_purge": 144,
        "btc_num_confirmations": 0,
        "btc_transaction_id": ""
    }

In this example, the next step would require the user to pay `25.233409` XMR to the Monero 
address `44TVPcCSHebEQp4LnapPkhb2pondb2Ed7GJJLc6TkKwtSyumUnQ6QzkCCkojZycH2MRfLcujCM7QR1gdnRULRraV4UpB5n4` 
while providing the payment ID `223907873a29a00e3a5ff563c3b65f278ab6eb0cba623428ca3d9aaa54ea7bbb`. 
The payment *must* be made before the order expires, in this case, inside `224` seconds.



Problems?
---------

Please check:

 - Are you including the proper parameters?
 - Are you using the proper request type `POST` vs. `GET`?
 - Are you setting ``"Content-Type: application/json"`` in headers?
 - Getting redirected? Add a ``/`` at the end of the API endpoint!

If none of this resolves the problem, please contact support.


Contact
-------

 * Follow us on Twitter: https://twitter.com/xmr_to
 * Bitcointalk support thread: https://bitcointalk.org/index.php?topic=959994
 * Monero forum support thread: https://forum.getmonero.org/3/merchants-and-marketplace/155/xmr-to-pay-any-bitcoin-address-anonymously-using-monero
 * XMR.TO support: support@xmr.to

