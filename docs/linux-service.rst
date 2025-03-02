.. _service-wireguard-linux-service:

Linux Setup
###########
5.6+ Kernels required unless explicitly backported.

.. code-block:: bash
  :caption: Install wireguard

  apt install wireguard

Autostart Tunnel as Service
***************************
Adapters may be autostarted as services.

.. code-block:: bash
  :caption: Start tunnel on boot.

  systemctl enable wg-quick@{ADAPTER}

.. code-block:: bash
  :caption: Start tunnel on demand.

  systemctl status/stop/start/restart wg-quick@{ADAPTER}

`Reference <https://community.hetzner.com/tutorials/install-and-configure-wireguard-vpn-debian>`__
