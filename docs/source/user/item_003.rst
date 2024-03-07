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


Step-by-step instructions
-------------------------
**Note** This only applies if your data were stored on the APS data magement system.

Please follow these steps:

#. login into Globus (with your personal globus credential)
#. go to "Collection/Search" and search for the aps data by selecting APS:DM plus the beamline (e.g. APS:DM:32ID)
#. login in the the APS data management system using the same badge number/password combination that use to access the APS poroposal system
#. if you forgot your password you can reset it `here <https://beam.aps.anl.gov/pls/apsweb/forgot_password.start_process>`_
#. go to / then seach for your data by PI_last_name-year-month/
#. set an end point on your computer (see `Globus EndPoint <https://www.globus.org/globus-connect-personal>`_) 
#. download the data!


For more information check the `APS data management documentation <https://git.aps.anl.gov/DM/dm-docs/-/wikis/DM/HowTos/Getting-Data-From-Globus>`_