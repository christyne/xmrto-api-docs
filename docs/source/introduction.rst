
Introduction
============

XMR.TO_ is a service that allows users to pay any bitcoin address
anonymously using Monero_. 
Monero_ is a secure, private, untraceable currency that is open-source
and freely available to all.
XMR.TO_ converts moneroj (XMR) provided by the user to bitcoins
(BTC), which are sent to a given bitcoin address.

XMR.TO_ provides an API that enables developers to use the service
in programs, apps, scripts etc. This API therefore allows developers
to integrate an option to pay any bitcoin address in their product,
for example, in a monero wallet service. 
The API is a REST-like API using `JSON` as dataformat. It is available anonymously
and does not require identification or authentification.
This document describes this API and gives examples of its usage.

Overview
--------

In order to use XMR.TO_, a user must first create an order over
a certain amount of bitcoins (BTC). Once the order has been created,
XMR.TO_ provides the user with the details for the payment in moneroj (XMR).
Once the user has fully paid using Monero, XMR.TO_ will create a bitcoin
transaction over the given amount to the given bitcoin address.

When using the API, the general flow of events is similar:

 - get service status to see if XMR.TO_ is available and to fetch current
   parameters (price, order limits, etc)
 - create a new order by supplying bitcoin destination address and mount
 - check order status to get payment information
 - pay order
 - continue to repeatedly check order status for processing progress

.. _XMR.TO: https://xmr.to
.. _Monero: https://getmonero.org

