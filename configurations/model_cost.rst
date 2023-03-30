.. _model_cost:

Cost of running the NorESM model
================================

Here we provide information on the cost of running the NorESM model for standard experiment configurations.

PES:
   Total processor count for simulation

Cost:
   Model cost, processing hours per simulated year

Thrput:
   Model throughput, simulated years per day

STime:
   Simulated time in (Y)ears, (M)onths or (D)ays

RTime:
   Model runtime in hours:minutes

Output:
   Storage size required for model output

.. table:: NorESM2-LM CMIP experiments on Betzy
+----------------+------------+------+---------+--------+-------+--------+--------+
|Experiment      | Grid       | PES  | Cost    | Thrput | STime | RTime  | Output |
+================+============+======+=========+========+=======+========+========+
| NHIST          | f19_tn14   | 1280 | 1487.53 |  20.65 |   10Y |  11:37 |   151G |
| NHIST          | f19_tn14   | 1280 | 1454.69 |  21.12 |   10Y |  11:21 |   151G |
+----------------+------------+------+---------+--------+-------+--------+--------+

.. table:: NorESM2 OMIP experiments on Betzy
+----------------+------------+------+---------+--------+-------+--------+--------+
|Experiment      | Grid       | PES  | Cost    | Thrput | STime | RTime  | Output |
+================+============+======+=========+========+=======+========+========+
| NOINYOC        | T62_tn21   |  512 |   57.37 | 214.21 |   10Y |   1:07 |    22G |
| NOINYOC        | T62_tn14   |  512 |  272.20 |  45.14 |   10Y |   5:19 |   193G |
| NOIIAOC20TR    | T62_tn21   |  512 |   68.32 | 179.87 |   10Y |   1:20 |    25G |
| NOIIAJRA       | TL319_tn14 |  512 |  550.56 |  22.32 |    1Y |   1:04 |   9.8G |
| NOIIAJRA       | TL319_tn14 |  512 |  634.72 |  19.36 |   10Y |  12:24 |   175G |
| NOIIAJRAOC20TR | TL319_tn14 |  512 |  688.06 |  17.06 |   10Y |  13:26 |   186G |
+----------------+------------+------+---------+--------+-------+--------+--------+
