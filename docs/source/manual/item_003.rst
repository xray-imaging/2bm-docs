Data Download
=============

At the beginning of the beamtime all users listed in the proposal receive an email with a direct link and instructions on how to download data from the APS.

Data sets are distributed using `Globus <https://www.globus.org>`_ a service that provides high-performance, secure, and reliable data transfer, sharing, and management capabilities for researchers, scientists, and other users with large-scale data needs. Globus data transfer specifically refers to the capability within the Globus service that allows users to transfer large volumes of data between different storage systems, whether they are located within the same institution or distributed across multiple institutions or even countries.

Key features of Globus data transfer include:

#. High Performance: Globus employs optimized protocols and infrastructure to achieve high-speed data transfer, enabling users to move large datasets efficiently.

#. Reliability: The service is designed to ensure data integrity and reliability, with mechanisms to automatically resume interrupted transfers and verify successful completion.

#. Security: Globus employs strong security measures to protect data during transfer, including encryption and authentication protocols to ensure that data remains secure and confidential.

#. Ease of Use: Globus provides a user-friendly interface that simplifies the process of initiating, monitoring, and managing data transfers, making it accessible to users with varying levels of technical expertise.

#. Integration: Globus integrates with various storage systems, including cloud storage, supercomputers, and institutional storage infrastructure, allowing users to transfer data seamlessly between different platforms.

Overall, Globus data transfer is a valuable tool for researchers, scientists, and other users who need to move large amounts of data efficiently and securely across different storage systems and geographic locations.


To use Globus you need to create a `Globus Account <https://docs.globus.org/how-to/get-started/>`_  and set up your computer as
a `Globus EndPoint <https://www.globus.org/globus-connect-personal>`_.


.. warning::

   APS grants access to the data collection using the email address provided in the proposal.
   You must ensure that this email address is linked to your Globus account before attempting
   to access the data. If your institutional email is not linked, follow the steps below to
   link it first.

Linking your proposal email to your Globus account
---------------------------------------------------

If the email address listed in your APS proposal is different from the one associated with
your primary Globus account, you need to link it as follows:

#. Log into `globus.org <https://www.globus.org>`_ with your existing Globus account
#. Click on your name (top right) and go to **Settings** → **Account** tab
#. Under **Identities**, click **"Link Another Identity"**
#. If your institution appears in the list, select it and log in with your institutional credentials
#. If your institution is not listed, look for the option to **link an email address** and enter the email used in the APS proposal
#. Globus will send a verification link to that address — click it to confirm
#. Once confirmed, the email identity is linked to your Globus account and APS will recognize you when you access the collection


Step-by-step instructions
-------------------------
**Note** This only applies if your data were stored on the APS data management system.

Please follow these steps:

#. login into Globus using your Globus account (see warning above to ensure the email in your APS proposal is linked to it)
#. go to "Collection/Search" and search for the aps data by selecting APS:DM plus the beamline (e.g. APS:DM:32ID)
#. login into the APS data management system using your Globus account when prompted
#. go to / then search for your data by PI_last_name-year-month/
#. set an end point on your computer (see `Globus EndPoint <https://www.globus.org/globus-connect-personal>`_)
#. download the data!


For more information check the `APS data management documentation <https://git.aps.anl.gov/DM/dm-docs/-/wikis/DM/HowTos/Getting-Data-From-Globus>`_
