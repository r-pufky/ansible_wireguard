.. _service-wireguard-key-generation:

Key Generation
##############
Public/Private keys need to be generated for each machine using wireguard. A
barebones utility is provided, generated keys are not OS specific.

.. code-block:: bash
  :caption: **0755 user user** wggen

  wg genkey | tee interface.key | wg pubkey > interface.pub
  wg genpsk > preshared.key
  chmod 0400 interface.{key,pub}

.. important::
  Standard precautions should be used for private key material. The private key
  will enable anyone to impersonate that client on the VPN.
