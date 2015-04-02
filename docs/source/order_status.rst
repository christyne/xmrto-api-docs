
Querying order status
=====================

Reference
---------

API endpoint: https://xmr.to/api/order_status/

The order status endpoint allows users to query the status of an order, thereby obtaining payment details and order processing progress.

Request
~~~~~~~

Issue a `POST` request to query the status of a given order.
You have to supply the order's ``uuid`` in the request:

.. sourcecode:: javascript

    {        
        "uuid": "<unique_order_identifier_as_12_character_string>",
    }


Response
~~~~~~~~

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
-------

Continuing from our previous example, we can query the order by supplying the order's unique identifier ``uuid``.

Request
~~~~~~~

::

    curl --data '{"uuid": "xmrto-VkT2yM"}' -H "Content-Type: application/json" \
        https://xmr.to/api/order_status/

Response
~~~~~~~~

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
address `44TV...B5n4` 
while providing the payment ID `2239...7bbb`. 
The payment **must** be made before the order expires, in this case, inside `224` seconds.


