.. |yes| image:: ../../../images/yes.png
.. |no| image:: ../../../images/no.png

.. role:: underline
   :class: underline

KlikAanKlikUit
==============

+------------------+-------------+
| **Feature**      | **Support** |
+------------------+-------------+
| Sending          | |no|        |
+------------------+-------------+
| Receiving        | |yes|       |
+------------------+-------------+
| Config           | |yes|       |
+------------------+-------------+

.. rubric:: Supported Brands

+------------------+--------------+
| **Brand**        | **Protocol** |
+------------------+--------------+
| KlikAanKlikUit   | kaku_motion  |
+------------------+--------------+

.. rubric:: Sender Arguments

*None*

.. rubric:: Config

.. code-block:: json
   :linenos:

   {
     "devices": {
       "contact": {
         "protocol": [ "kaku_motion" ],
         "id": [{
           "id": 100,
           "unit": 0
         }],
         "state": "on"
        }
     }
   }

+------------------+-----------------+
| **Option**       | **Value**       |
+------------------+-----------------+
| unitcode         | 0 - 67108863    |
+------------------+-----------------+
| state            | on / off        |
+------------------+-----------------+

.. rubric:: Optional Settings

*None*

.. rubric:: Protocol

This protocol sends 148 pulses like this

.. code-block:: guess

   286 2825 286 201 289 1337 287 209 283 1351 287 204 289 1339 288 207 288 1341 289 207 281 1343 284 205 292 1346 282 212 283 1348 282 213 279 1352 282 211 281 1349 282 210 283 1347 284 211 288 1348 281 211 285 1353 278 213 280 1351 280 232 282 1356 279 213 285 1351 276 215 285 1348 277 216 278 1359 278 216 279 1353 272 214 283 1358 276 216 276 1351 278 214 284 1357 275 217 276 1353 270 217 277 1353 272 220 277 1351 275 220 272 1356 275 1353 273 224 277 236 282 1355 272 1353 273 233 273 222 268 1358 270 219 277 1361 274 218 280 1358 272 1355 271 243 251 11302

The first 2 pulses are the ``header`` and the last 2 pulses are the ``footer``. These are meant to identify the pulses as genuine. We don't use them for further processing. The next step is to transform this output into 36 groups of 4 pulses (and thereby dropping the ``header`` and ``footer`` pulses).

.. code-block:: guess

   286 201 289 1337
   287 209 283 1351
   287 204 289 1339
   288 207 288 1341
   289 207 281 1343
   284 205 292 1346
   282 212 283 1348
   282 213 279 1352
   282 211 281 1349
   282 210 283 1347
   284 211 288 1348
   281 211 285 1353
   278 213 280 1351
   280 232 282 1356
   279 213 285 1351
   276 215 285 1348
   277 216 278 1359
   278 216 279 1353
   272 214 283 1358
   276 216 276 1351
   278 214 284 1357
   275 217 276 1353
   270 217 277 1353
   272 220 277 1351
   275 220 272 1356
   275 1353 273 224
   277 236 282 1355
   272 1353 273 233
   273 222 268 1358
   270 219 277 1361
   274 218 280 1358
   272 1355 271 243

If we now look at carefully at these groups you can distinguish two types of groups:

#. 272 1355 271 243
#. 274 218 280 1358

So the first group is defined by a high 2nd and the second group has a high 4th pulse. So we take either of these two pulses to define a 0 or a 1. In this case we say a high 2nd pulse means a 1 and a low 2nd pulse means a 0. We then get the following output:

.. code-block:: guess

   00000000000000000000000001010001

Each (group) of numbers has a specific meaning:

- ID: 0 till 25(Convert the binary code to decimal to get the correct id)
- All: 26
- State: 27
- Unit: 28 till 31

.. code-block:: guess

   00000000000000000000000001 0 1 0001

- The ``ID`` is defined as a binary number
- The ``All`` tells us if a code was meant for all devices with the same ID
- The ``State`` defines whether a devices is opened or closed
- The ``Unit`` is also defined as a binary number

So this code represents:

- ID: 1
- All: Single
- State: On
- Unit: 1

Another example:

- ID: 123456
- All: All
- State: Off
- Unit: 15

.. code-block:: guess

   00000000011110001001000000101111